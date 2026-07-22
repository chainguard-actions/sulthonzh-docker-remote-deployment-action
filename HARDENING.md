<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.44

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.44** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all three workflow files are pinned to mutable tags or branch names rather than immutable 40-character commit SHAs. This exposes the workflows to supply-chain attacks if any referenced action is compromised or its tag is moved.

Failing references in ci.yml: actions/checkout@v4 (×5).
Failing references in code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×8).
Failing references in release.yml: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:34`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:23`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:56`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/code-review.yml:113`
- `.github/workflows/release.yml:15`
- `.github/workflows/release.yml:19`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:39`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:79`
- `.github/workflows/release.yml:97`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.) and violates the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references across ci.yml, code-review.yml, and release.yml by replacing mutable tags/branch names with immutable 40-character commit SHAs (preserving the original tag in a comment). Added `permissions: {}` top-level block to ci.yml to enforce least privilege — the CI jobs only perform linting and build checks requiring no GitHub token permissions. The release.yml already had appropriate job-level permissions (contents: read/write, packages: write) which were preserved.

