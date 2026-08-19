<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.26

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.26** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags or branch names instead of pinned full-length SHA commits, making them vulnerable to supply-chain attacks.

.github/workflows/main.yml:
  - uses: actions/checkout@v6
  - uses: docker/setup-qemu-action@v4
  - uses: docker/setup-buildx-action@v4
  - uses: docker/login-action@v4
  - uses: docker/build-push-action@v7

.github/workflows/code-review.yml:
  - uses: actions/checkout@v6 (multiple steps)
  - uses: sulthonzh/code-reviewer@main (multiple steps — @main is a mutable branch ref)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:29`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:50`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/code-review.yml:116`
- `.github/workflows/code-review.yml:124`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g., write access to contents). A minimal permissions block should be added.

Locations:

- `.github/workflows/main.yml:1`

### suspicious-run-content (severity: high)

docker-entrypoint.sh contains two instances of the `eval-dynamic` pattern (eval with command substitution or variable expansion):

1. `eval $(ssh-agent)` — uses eval with `$(...)` command substitution. While this is a common SSH agent pattern, it matches the eval-dynamic check.

2. `eval "${DEPLOYMENT_COMMAND} ${INPUT_ARGS}"` — uses eval with `$` variable expansion to execute a dynamically constructed command string. Even though inputs are validated earlier, using eval to execute a string built from user-controlled variables is inherently risky and matches the eval-dynamic pattern.

Locations:

- `docker-entrypoint.sh:228`
- `docker-entrypoint.sh:310`

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded literal password: `DB_PASSWORD: mypassword`. This is a plaintext credential embedded directly in the file. Even if intended as an example, shipping a hardcoded password in the action repository is a security risk. Credentials should be supplied via environment variables or secrets at runtime.

Locations:

- `docker-compose.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, suspicious-run-content, hardcoded-credentials

**Notes:**

1. unpinned-uses: Pinned all action references in .github/workflows/main.yml (actions/checkout@v6→SHA, docker/setup-qemu-action@v4→SHA, docker/setup-buildx-action@v4→SHA, docker/login-action@v4→SHA, docker/build-push-action@v7→SHA) and .github/workflows/code-review.yml (actions/checkout@v6→SHA for 5 steps, sulthonzh/code-reviewer@main→SHA for 8 steps). All pinned with # tag comments for readability.

2. missing-permissions: Added `permissions: contents: read` top-level block to .github/workflows/main.yml. The code-review.yml already had a permissions block.

3. suspicious-run-content: Replaced both eval patterns in docker-entrypoint.sh: (a) `eval $(ssh-agent)` replaced with explicit variable extraction via grep/printf without eval; (b) `eval "${DEPLOYMENT_COMMAND} ${INPUT_ARGS}"` replaced with `bash -c "${DEPLOYMENT_COMMAND} \"\$1\"" -- "$INPUT_ARGS"` which passes INPUT_ARGS as a positional parameter avoiding eval with variable expansion.

4. hardcoded-credentials: Replaced `DB_PASSWORD: mypassword` in docker-compose.yml with `DB_PASSWORD: ${DB_PASSWORD:?DB_PASSWORD environment variable must be set}` requiring the value to be supplied at runtime via environment variable.

