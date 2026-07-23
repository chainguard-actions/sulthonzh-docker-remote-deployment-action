<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.51

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.51** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions using mutable tags or branch names instead of immutable 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or the tag is moved.

.github/workflows/ci.yml: actions/checkout@v4 (×5 steps)

.github/workflows/code-review.yml: actions/checkout@v6 (×5 steps), sulthonzh/code-reviewer@main (×7 steps — branch ref is especially dangerous)

.github/workflows/release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:44`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:32`
- `.github/workflows/code-review.yml:35`
- `.github/workflows/code-review.yml:47`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:84`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:65`
- `.github/workflows/release.yml:79`

### missing-permissions (severity: medium)

The workflow file .github/workflows/ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.).

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by pinning each to its full 40-character SHA (with the original tag preserved as a comment). Added `permissions: {}` top-level block to ci.yml to address the missing-permissions finding. Specific changes: ci.yml — pinned actions/checkout@v4 (×5) to SHA 11d5960a..., added `permissions: {}`; code-review.yml — pinned actions/checkout@v6 (×5) to SHA d23441a4... and sulthonzh/code-reviewer@main (×7) to SHA d882af6c...; release.yml — pinned actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, and softprops/action-gh-release@v1 to their respective SHAs.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed github-env-injection in .github/workflows/release.yml 'Generate changelog' step:
1. Sanitized $TAG (derived from GITHUB_REF) using `printf '%s' "$TAG" | tr -d '\n\r'` before writing `tag=` to $GITHUB_OUTPUT.
2. Sanitized $CHANGELOG (from git log commit messages) using `printf '%s' "$CHANGELOG" | tr -d '\r' | grep -v '^CHANGELOG_EOF$'` to remove carriage returns and prevent heredoc delimiter injection. Also changed the heredoc delimiter from the generic 'EOF' to the more unique 'CHANGELOG_EOF' to reduce collision risk.

