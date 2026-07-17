<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.39

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.39** was hardened automatically. 2 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in the workflow files use mutable tag or branch refs instead of immutable 40-character SHA digests, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (×5 steps)

code-review.yml: actions/checkout@v6 (×multiple steps), sulthonzh/code-reviewer@main (×multiple steps — also uses a branch ref 'main' which is especially dangerous)

release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:23`
- `.github/workflows/code-review.yml:37`
- `.github/workflows/code-review.yml:42`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:90`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:109`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:31`
- `.github/workflows/release.yml:37`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:68`
- `.github/workflows/release.yml:96`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. This means the workflow runs with the default GitHub token permissions, which may be broader than necessary (e.g., write access to contents and packages on some repository configurations).

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references across ci.yml, code-review.yml, and release.yml by replacing mutable tags/branches with full 40-character SHA digests (preserving the original tag/branch as a comment). Added `permissions: {}` top-level block to ci.yml since its jobs only run local shell commands and docker builds requiring no GitHub token permissions. All 26 unpinned action references have been pinned using SHAs resolved via lookup_action_sha.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed the 'Generate changelog' step in .github/workflows/release.yml:
1. TAG sanitization (line 63): Added `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` and write `safe_tag` instead of raw `$TAG` to $GITHUB_OUTPUT.
2. CHANGELOG sanitization (line 72): Added `safe_changelog=$(printf '%s' "$CHANGELOG" | tr -d '\r')` to strip carriage returns. Changed the heredoc delimiter from `EOF` to `CHANGELOG_EOF` to prevent a commit message containing a bare 'EOF' line from prematurely terminating the heredoc. Used `printf '%s\n'` to write the sanitized changelog content.

### Iteration 3

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` on line 218 of docker-entrypoint.sh with a safer alternative that starts ssh-agent with the `-s` flag, captures its output into a variable, then explicitly parses SSH_AUTH_SOCK and SSH_AGENT_PID using grep with Perl-compatible lookbehind regex, and exports them directly. This eliminates the eval-with-command-substitution pattern while preserving identical runtime behavior.

