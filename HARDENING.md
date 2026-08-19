<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.7** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable version tags or branch names instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/main.yml:
- `actions/checkout@v6`
- `docker/setup-qemu-action@v4`
- `docker/setup-buildx-action@v4`
- `docker/login-action@v4`
- `docker/build-push-action@v7`

.github/workflows/code-review.yml:
- `actions/checkout@v6` (used in multiple jobs)
- `sulthonzh/code-reviewer@main` (used in multiple steps — pinned to a mutable branch name)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:28`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:62`
- `.github/workflows/code-review.yml:71`
- `.github/workflows/code-review.yml:79`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/main.yml` has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g., `contents: write` by default on some repositories). Explicit minimal permissions should be declared.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references in both workflow files by resolving them to full 40-character commit SHAs using lookup_action_sha. In main.yml: pinned actions/checkout@v6, docker/setup-qemu-action@v4, docker/setup-buildx-action@v4, docker/login-action@v4, and docker/build-push-action@v7. In code-review.yml: pinned actions/checkout@v6 (5 occurrences) and sulthonzh/code-reviewer@main (6 occurrences). Also added a top-level `permissions: contents: read` block to main.yml to fix the missing-permissions finding. The code-review.yml already had a permissions block and was not modified for that finding.

### Iteration 2

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative: captures `ssh-agent -s` output into a variable, then uses `sed` to parse SSH_AUTH_SOCK and SSH_AGENT_PID values explicitly, exports them, and unsets the temporary variable. This eliminates the eval-with-command-substitution pattern while preserving the same SSH agent initialization behavior.

