<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable tags or branch names instead of immutable 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced action is compromised or its tag is moved.

In `.github/workflows/main.yml`:
- `actions/checkout@v6`
- `docker/setup-qemu-action@v4`
- `docker/setup-buildx-action@v4`
- `docker/login-action@v4`
- `docker/build-push-action@v7`

In `.github/workflows/code-review.yml`:
- `actions/checkout@v6` (multiple steps)
- `sulthonzh/code-reviewer@main` (multiple steps — branch ref is especially dangerous)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:21`
- `.github/workflows/main.yml:25`
- `.github/workflows/main.yml:31`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:35`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:74`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:99`
- `.github/workflows/code-review.yml:107`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/main.yml` has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes by default in many configurations). A minimal permissions block such as `permissions: contents: read` should be added.

Locations:

- `.github/workflows/main.yml:1`

### suspicious-run-content (severity: high)

The entrypoint script `docker-entrypoint.sh` contains `eval $(ssh-agent)`, which matches the `eval-dynamic` pattern (`eval` followed by `$(...)`). This pattern dynamically constructs and executes shell commands via command substitution, which is a recognised obfuscation/injection vector. While `ssh-agent` is a known binary, the use of `eval $(...)` is flagged because the output of the subshell is evaluated as shell code without any validation of what `ssh-agent` emits. The safer alternative is to use `ssh-agent` with explicit variable assignment: `SSH_AGENT_OUTPUT=$(ssh-agent); eval "$SSH_AGENT_OUTPUT"` with the output validated, or use a dedicated SSH agent setup mechanism.

Locations:

- `docker-entrypoint.sh:116`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action references in .github/workflows/main.yml (actions/checkout@v6, docker/setup-qemu-action@v4, docker/setup-buildx-action@v4, docker/login-action@v4, docker/build-push-action@v7) and .github/workflows/code-review.yml (actions/checkout@v6 ×5, sulthonzh/code-reviewer@main ×7) to their full 40-character commit SHAs, preserving the original tag as an inline comment.
2. missing-permissions: Added `permissions: contents: read` top-level block to .github/workflows/main.yml.
3. suspicious-run-content: Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with the safer two-step pattern: capture output into SSH_AGENT_OUTPUT variable first, then `eval "$SSH_AGENT_OUTPUT"`, eliminating the direct eval-of-command-substitution pattern.

