<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.18

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.18** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag or branch is updated with malicious code.

.github/workflows/main.yml unpinned refs:
- uses: actions/checkout@v6
- uses: docker/setup-qemu-action@v4
- uses: docker/setup-buildx-action@v4
- uses: docker/login-action@v4
- uses: docker/build-push-action@v7

.github/workflows/code-review.yml unpinned refs:
- uses: actions/checkout@v6 (multiple steps)
- uses: sulthonzh/code-reviewer@main (multiple steps — branch ref)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:24`
- `.github/workflows/main.yml:29`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:69`
- `.github/workflows/code-review.yml:76`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.).

Locations:

- `.github/workflows/main.yml:1`

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded literal password value `DB_PASSWORD: mypassword`. Even though this file is used as an example/template, hardcoded credentials in the repository can be accidentally used in production or leak sensitive patterns. The value should be replaced with an environment variable reference or secret.

Locations:

- `docker-compose.yml:46`

### suspicious-run-content (severity: high)

docker-entrypoint.sh contains two `eval` usages that match the `eval-dynamic` pattern:

1. `eval $(ssh-agent)` — eval with command substitution `$(...)`. Pattern: `eval\s+[\x60$]`.

2. `eval "${DEPLOYMENT_COMMAND} ${INPUT_ARGS}"` — eval of a dynamically-constructed string that incorporates user-controlled inputs (`INPUT_ARGS` and `DEPLOYMENT_COMMAND` which is built from `INPUT_DEPLOYMENT_MODE`, `INPUT_DEPLOY_PATH`, `INPUT_STACK_FILE_NAME`, `INPUT_REMOTE_DOCKER_HOST`, `INPUT_REMOTE_DOCKER_PORT`). Although input validation is attempted earlier, using `eval` to execute a string assembled from external inputs is inherently risky and matches the `eval-dynamic` sub-check.

Locations:

- `docker-entrypoint.sh:168`
- `docker-entrypoint.sh:222`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action refs in .github/workflows/main.yml (actions/checkout@v6→SHA, docker/setup-qemu-action@v4→SHA, docker/setup-buildx-action@v4→SHA, docker/login-action@v4→SHA, docker/build-push-action@v7→SHA) and .github/workflows/code-review.yml (actions/checkout@v6→SHA ×5, sulthonzh/code-reviewer@main→SHA ×7). Original tags preserved as inline comments.
2. missing-permissions: Added top-level `permissions: contents: read / packages: write` to main.yml (minimum needed for checkout + Docker Hub push).
3. hardcoded-credentials: Replaced `DB_PASSWORD: mypassword` in docker-compose.yml with `DB_PASSWORD: ${DB_PASSWORD}` to read from environment at runtime.
4. suspicious-run-content: Replaced both eval usages in docker-entrypoint.sh — (a) `eval $(ssh-agent)` replaced with capturing output to a variable and extracting SSH_AUTH_SOCK/SSH_AGENT_PID via grep+export; (b) `eval "${DEPLOYMENT_COMMAND} ${INPUT_ARGS}"` replaced with bash array splitting via `read -ra` and direct array execution.

