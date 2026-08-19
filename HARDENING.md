<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.17

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.17** was hardened automatically. 2 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files reference Actions using mutable version tags or branch names instead of full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved to point at malicious code.

.github/workflows/main.yml:
  - uses: actions/checkout@v6
  - uses: docker/setup-qemu-action@v4
  - uses: docker/setup-buildx-action@v4
  - uses: docker/login-action@v4
  - uses: docker/build-push-action@v7

.github/workflows/code-review.yml:
  - uses: actions/checkout@v6  (multiple occurrences)
  - uses: sulthonzh/code-reviewer@main  (multiple occurrences — mutable branch ref)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:28`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:56`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:76`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:91`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow runs with the default (potentially broad) token permissions, which may include write access to repository contents and packages.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references in both workflow files by pinning to full 40-character commit SHAs (with mutable tag preserved as a comment for readability). Actions pinned: actions/checkout@v6→d23441a4, docker/setup-qemu-action@v4→96fe6ef7, docker/setup-buildx-action@v4→bb05f3f5, docker/login-action@v4→af1e73f9, docker/build-push-action@v7→53b7df96, sulthonzh/code-reviewer@main→d882af6c (6 occurrences). Added a top-level permissions block to main.yml with 'contents: read' and 'packages: write' — the minimum needed for checkout and pushing Docker images. code-review.yml already had a permissions block and was not changed in that regard.

### Iteration 2

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced the hardcoded plaintext password `DB_PASSWORD: mypassword` in hardened/action/docker-compose.yml (line 44) with `DB_PASSWORD: ${DB_PASSWORD}`. Docker Compose's variable substitution syntax now requires the password to be supplied at runtime via an environment variable or a `.env` file, eliminating the embedded plaintext credential.

### Iteration 1

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` at line 170 of docker-entrypoint.sh with a safe alternative: capture `ssh-agent -s` output into a variable, then parse SSH_AUTH_SOCK and SSH_AGENT_PID using grep with fixed patterns, and export them explicitly. This eliminates the eval-dynamic pattern while preserving SSH agent initialization functionality.

