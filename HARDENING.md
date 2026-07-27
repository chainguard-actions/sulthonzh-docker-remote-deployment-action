<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.66

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.66** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files use mutable tag or branch refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/ci.yml: uses: actions/checkout@v4 (×5 occurrences)

.github/workflows/code-review.yml: uses: actions/checkout@v6 (×4 occurrences), uses: sulthonzh/code-reviewer@main (×8 occurrences — branch ref)

.github/workflows/release.yml: uses: actions/checkout@v4, uses: docker/setup-qemu-action@v3, uses: docker/setup-buildx-action@v3, uses: docker/login-action@v3 (×2), uses: docker/metadata-action@v5, uses: docker/build-push-action@v5, uses: softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:42`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:24`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:41`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:86`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:36`
- `.github/workflows/release.yml:44`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:72`

### missing-permissions (severity: medium)

The workflow file .github/workflows/ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. This means the workflow runs with the default token permissions, which may be broader than necessary.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by replacing mutable tags/branches with pinned 40-character SHA commits (with tag comments for readability). Added top-level `permissions: contents: read` to ci.yml which had no permissions block. Pinned actions: actions/checkout@v4 (SHA: 11d5960...), actions/checkout@v6 (SHA: d23441a...), sulthonzh/code-reviewer@main (SHA: d882af6...), docker/setup-qemu-action@v3 (SHA: c7c5346...), docker/setup-buildx-action@v3 (SHA: 8d2750c...), docker/login-action@v3 (SHA: c94ce9f...), docker/metadata-action@v5 (SHA: c299e40...), docker/build-push-action@v5 (SHA: ca052bb...), softprops/action-gh-release@v1 (SHA: de2c0eb...).

