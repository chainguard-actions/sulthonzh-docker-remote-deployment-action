<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.22

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.22** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

In .github/workflows/main.yml:
- uses: actions/checkout@v6
- uses: docker/setup-qemu-action@v4
- uses: docker/setup-buildx-action@v4
- uses: docker/login-action@v4
- uses: docker/build-push-action@v7

In .github/workflows/code-review.yml:
- uses: actions/checkout@v6 (multiple occurrences)
- uses: sulthonzh/code-reviewer@main (multiple occurrences)

Locations:

- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:16`
- `.github/workflows/main.yml:19`
- `.github/workflows/main.yml:22`
- `.github/workflows/main.yml:28`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/code-review.yml:34`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:50`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:99`
- `.github/workflows/code-review.yml:108`
- `.github/workflows/code-review.yml:115`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. This means the workflow runs with the default (broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/main.yml:1`

### hardcoded-credentials (severity: high)

The file docker-compose.yml contains a hardcoded literal password value: `DB_PASSWORD: mypassword`. This is a plaintext credential embedded directly in a source file. Even if this is an example/demo file, it should use environment variable references or secrets rather than literal values.

Locations:

- `docker-compose.yml:50`

### suspicious-run-content (severity: high)

Sub-check: eval-dynamic. The file docker-entrypoint.sh contains `eval $(ssh-agent)`, which uses `eval` with command substitution (`$(...)`). This matches the eval-dynamic pattern (`eval\s+[$\x60]`). While `ssh-agent` is a known-safe binary in this context, the pattern is flagged because it executes dynamically constructed shell commands via eval with command substitution, which is a recognized obfuscation/injection vector.

Locations:

- `docker-entrypoint.sh:196`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action refs in .github/workflows/main.yml (actions/checkout@v6→SHA, docker/setup-qemu-action@v4→SHA, docker/setup-buildx-action@v4→SHA, docker/login-action@v4→SHA, docker/build-push-action@v7→SHA) and .github/workflows/code-review.yml (actions/checkout@v6→SHA, sulthonzh/code-reviewer@main→SHA for all 9 occurrences). Original tags preserved as inline comments.
2. missing-permissions: Added top-level `permissions: contents: read` to .github/workflows/main.yml (minimal permission needed for the checkout + Docker build/push workflow).
3. hardcoded-credentials: Replaced `DB_PASSWORD: mypassword` in docker-compose.yml with `DB_PASSWORD: ${DB_PASSWORD}` so the value is sourced from the environment rather than hardcoded in the file.
4. suspicious-run-content: Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative that writes ssh-agent -s output to a temp file and sources it with the POSIX `.` builtin, then removes the temp file. This eliminates the eval+command-substitution pattern while preserving the same SSH agent initialization behavior.

