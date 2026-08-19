<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.19

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.19** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks.

.github/workflows/main.yml:
- `actions/checkout@v6` (tag)
- `docker/setup-qemu-action@v4` (tag)
- `docker/setup-buildx-action@v4` (tag)
- `docker/login-action@v4` (tag)
- `docker/build-push-action@v7` (tag)

.github/workflows/code-review.yml:
- `actions/checkout@v6` (tag, used multiple times)
- `sulthonzh/code-reviewer@main` (branch, used multiple times)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:28`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:66`
- `.github/workflows/code-review.yml:72`

### permissions (severity: medium)

`.github/workflows/main.yml` has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g., write access to contents). A minimal permissions block should be added.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions

**Notes:**

Fixed all unpinned `uses:` references in both workflow files by pinning them to full 40-character commit SHAs (with original tag/branch preserved as comments): actions/checkout@v6→df4cb1c, docker/setup-qemu-action@v4→96fe6ef, docker/setup-buildx-action@v4→bb05f3f, docker/login-action@v4→af1e73f, docker/build-push-action@v7→53b7df9, sulthonzh/code-reviewer@main→d882af6 (7 occurrences across code-review.yml). Added a minimal top-level `permissions: contents: read` block to main.yml, which previously had no permissions block. The code-review.yml already had explicit permissions defined so no change was needed there.

### Iteration 1

**Fixes applied:** hardcoded-credentials, suspicious-run-content

**Notes:**

1. docker-compose.yml line 47: Replaced hardcoded `DB_PASSWORD: mypassword` with `DB_PASSWORD: ${DB_PASSWORD:?DB_PASSWORD environment variable must be set}` — the value must now be supplied via an environment variable at runtime; Docker Compose will fail with a descriptive error if it is absent. 2. docker-entrypoint.sh line 163: Replaced `eval $(ssh-agent)` with a safe pattern that captures ssh-agent -s output into a shell variable and extracts only the known SSH_AUTH_SOCK and SSH_AGENT_PID values using grep -oP, then exports them explicitly. This eliminates the eval-of-command-substitution pattern without changing the functional behaviour of the SSH agent setup.

