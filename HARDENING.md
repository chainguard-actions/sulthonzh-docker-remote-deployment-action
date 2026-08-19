<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.25

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.25** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks.

.github/workflows/main.yml: actions/checkout@v6, docker/setup-qemu-action@v4, docker/setup-buildx-action@v4, docker/login-action@v4, docker/build-push-action@v7

.github/workflows/code-review.yml: actions/checkout@v6 (multiple steps), sulthonzh/code-reviewer@main (multiple steps — also uses a mutable branch ref)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:21`
- `.github/workflows/main.yml:25`
- `.github/workflows/main.yml:31`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:35`
- `.github/workflows/code-review.yml:47`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:85`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/code-review.yml:108`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g., write access to contents). A minimal permissions block such as `permissions: contents: read` should be added.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references in both workflow files by replacing mutable tags/branches with full 40-character commit SHAs (preserving original tags as comments). Specifically: actions/checkout@v6, docker/setup-qemu-action@v4, docker/setup-buildx-action@v4, docker/login-action@v4, docker/build-push-action@v7 in main.yml; and actions/checkout@v6 (5 occurrences) and sulthonzh/code-reviewer@main (9 occurrences) in code-review.yml. Also added a top-level `permissions: contents: read` block to main.yml to address the missing-permissions finding. The code-review.yml already had an explicit permissions block so no change was needed there.

### Iteration 2

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative that explicitly parses the ssh-agent output. The new code: (1) captures `ssh-agent -s` output into a temp variable, (2) extracts SSH_AUTH_SOCK and SSH_AGENT_PID using grep -oP with lookbehind patterns, (3) exports the variables explicitly, and (4) unsets the temp variable. This eliminates the eval-dynamic pattern (eval with command substitution) while preserving identical SSH agent setup functionality.

