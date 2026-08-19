<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.12

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.12** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the upstream action is compromised or the tag is moved.

In .github/workflows/main.yml:
- uses: actions/checkout@v6
- uses: docker/setup-qemu-action@v4
- uses: docker/setup-buildx-action@v4
- uses: docker/login-action@v4
- uses: docker/build-push-action@v7

In .github/workflows/code-review.yml:
- uses: actions/checkout@v6 (multiple steps)
- uses: sulthonzh/code-reviewer@main (multiple steps — branch ref)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:24`
- `.github/workflows/main.yml:30`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:47`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:89`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:110`

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded plaintext password: `DB_PASSWORD: mypassword`. This literal credential value is committed to the repository and should be replaced with a secret reference or environment variable injected at runtime.

Locations:

- `docker-compose.yml:47`

### missing-permissions (severity: medium)

.github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes by default on many repositories).

Locations:

- `.github/workflows/main.yml:1`

### suspicious-run-content (severity: high)

docker-entrypoint.sh (the Docker action's entrypoint script) contains `eval $(ssh-agent)`, which matches the eval-dynamic pattern (`eval $(...)`). This dynamically constructs and executes shell commands via command substitution. While `ssh-agent` is a known tool, this pattern is flagged because it executes the output of a command substitution via eval, which is a common obfuscation/injection vector. Sub-check: eval-dynamic.

Locations:

- `docker-entrypoint.sh:162`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, hardcoded-credentials, missing-permissions, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action refs in .github/workflows/main.yml (actions/checkout@v6→SHA, docker/setup-qemu-action@v4→SHA, docker/setup-buildx-action@v4→SHA, docker/login-action@v4→SHA, docker/build-push-action@v7→SHA) and .github/workflows/code-review.yml (actions/checkout@v6→SHA ×5, sulthonzh/code-reviewer@main→SHA ×7). Original tags preserved as inline comments.
2. hardcoded-credentials: Replaced `DB_PASSWORD: mypassword` in docker-compose.yml with `DB_PASSWORD: ${DB_PASSWORD:?DB_PASSWORD environment variable is required}` — the value must now be injected at runtime via environment variable.
3. missing-permissions: Added top-level `permissions: contents: read` to .github/workflows/main.yml (the minimum needed for checkout on a release workflow).
4. suspicious-run-content: Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative: `ssh-agent -a "$SSH_AGENT_SOCK"` bound to a mktemp-generated socket path, then `export SSH_AUTH_SOCK="$SSH_AGENT_SOCK"` — no eval needed.

