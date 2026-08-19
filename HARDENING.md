<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.21

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.21** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### suspicious-run-content (severity: high)

The docker-entrypoint.sh script uses `eval $(ssh-agent)` which matches the eval-dynamic pattern (eval with command substitution). This executes dynamically constructed shell commands and is a recognized obfuscation/injection risk pattern.

Locations:

- `docker-entrypoint.sh:196`

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded literal password: `DB_PASSWORD: mypassword`. This is a non-expression literal value assigned to a name containing 'password'.

Locations:

- `docker-compose.yml:46`

### unpinned-uses (severity: high)

Workflow files reference actions using mutable version tags instead of full 40-character SHA digests, making them vulnerable to supply-chain attacks.

main.yml unpinned references:
- actions/checkout@v6
- docker/setup-qemu-action@v4
- docker/setup-buildx-action@v4
- docker/login-action@v4
- docker/build-push-action@v7

code-review.yml unpinned references:
- actions/checkout@v6 (used in multiple jobs)
- sulthonzh/code-reviewer@main (used in multiple steps across multiple jobs)

Locations:

- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:16`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:24`
- `.github/workflows/main.yml:29`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:49`
- `.github/workflows/code-review.yml:59`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:90`
- `.github/workflows/code-review.yml:101`
- `.github/workflows/code-review.yml:109`
- `.github/workflows/code-review.yml:118`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and the single `build` job also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** suspicious-run-content, hardcoded-credentials, unpinned-uses, missing-permissions

**Notes:**

1. docker-entrypoint.sh: Replaced `eval $(ssh-agent)` with a safe pattern that captures ssh-agent output into a variable and parses SSH_AUTH_SOCK and SSH_AGENT_PID using grep -oP, then exports them explicitly — no eval needed.
2. docker-compose.yml: Replaced hardcoded `DB_PASSWORD: mypassword` with `DB_PASSWORD: ${DB_PASSWORD}` so the value is sourced from the runtime environment.
3. .github/workflows/main.yml: Pinned all 5 action references to full SHA digests (actions/checkout, docker/setup-qemu-action, docker/setup-buildx-action, docker/login-action, docker/build-push-action) and added a minimal `permissions:` block (contents: read, packages: write).
4. .github/workflows/code-review.yml: Pinned all 11 action references to full SHA digests (actions/checkout@v6 → SHA, sulthonzh/code-reviewer@main → SHA). The file already had a permissions block so no change was needed there.

