<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.36

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.36** was hardened automatically. 2 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files use mutable tag or branch refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (×5)
code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×7 — mutable branch ref)
release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:66`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:99`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/code-review.yml:115`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:47`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:82`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the default repository token permissions, which may be overly broad (write access to contents, packages, etc.).

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by resolving each tag/branch to its full 40-character SHA commit hash (preserving the original tag in a comment). Added `permissions: {}` top-level block to ci.yml which had no permissions defined. Specific changes: ci.yml — 5× actions/checkout@v4 pinned to SHA 11d5960a..., plus added `permissions: {}`; code-review.yml — 5× actions/checkout@v6 pinned to SHA d23441a4..., 7× sulthonzh/code-reviewer@main pinned to SHA d882af6c...; release.yml — actions/checkout@v4 (×2) pinned to SHA 11d5960a..., docker/setup-qemu-action@v3 pinned to SHA c7c53464..., docker/setup-buildx-action@v3 pinned to SHA 8d2750c6..., docker/login-action@v3 (×2) pinned to SHA c94ce9fb..., docker/metadata-action@v5 pinned to SHA c299e40c..., docker/build-push-action@v5 pinned to SHA ca052bb5..., softprops/action-gh-release@v1 pinned to SHA de2c0eb8...

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection vulnerabilities in .github/workflows/release.yml 'Generate changelog' step:
1. TAG write (line 67): Added `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` sanitization before writing to $GITHUB_OUTPUT to prevent newline injection via crafted tag names.
2. CHANGELOG heredoc write (line 76): Added `safe_changelog=$(printf '%s' "$CHANGELOG" | tr -d '\r')` to strip carriage returns from attacker-controlled git commit messages, and replaced the static 'EOF' heredoc delimiter with a randomly generated one (`DELIM="EOF_$(openssl rand -hex 8)"`) to prevent a crafted commit message from breaking the heredoc delimiter and injecting arbitrary content into $GITHUB_OUTPUT.

### Iteration 1

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative that captures ssh-agent output into a variable and extracts SSH_AUTH_SOCK and SSH_AGENT_PID using sed, then exports them explicitly. This eliminates the eval-dynamic pattern (eval with command substitution) while preserving the SSH agent initialization functionality.

