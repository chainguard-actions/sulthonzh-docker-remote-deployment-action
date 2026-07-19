<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.58

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.58** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files use mutable tag or branch refs instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

**ci.yml** — all 5 `uses:` references are unpinned:
- `actions/checkout@v4` (×5)

**code-review.yml** — all 12 `uses:` references are unpinned:
- `actions/checkout@v6` (×5)
- `sulthonzh/code-reviewer@main` (×7, including auto-merge and auto-release steps)

**release.yml** — all 9 `uses:` references are unpinned:
- `actions/checkout@v4`
- `docker/setup-qemu-action@v3`
- `docker/setup-buildx-action@v3`
- `docker/login-action@v3` (×2)
- `docker/metadata-action@v5`
- `docker/build-push-action@v5`
- `softprops/action-gh-release@v1`

All should be pinned to full 40-character commit SHAs (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:50`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:70`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:86`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/code-review.yml:102`
- `.github/workflows/code-review.yml:112`
- `.github/workflows/code-review.yml:119`
- `.github/workflows/release.yml:12`
- `.github/workflows/release.yml:16`
- `.github/workflows/release.yml:19`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:35`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:80`

### missing-permissions (severity: medium)

The CI workflow file `.github/workflows/ci.yml` has no top-level `permissions:` key and none of its five jobs (`shell-lint`, `dockerfile-lint`, `validate-yaml`, `security-scan`, `build-image`) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be broader than necessary (e.g. write access to contents). A minimal `permissions: read-all` or specific per-job scopes should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 26 unpinned `uses:` references across three workflow files by pinning each to its full 40-character commit SHA (with the original tag preserved as a comment). Added a top-level `permissions: contents: read` block to ci.yml to satisfy the missing-permissions finding. The code-review.yml already had a permissions block so no change was needed there. The release.yml already had job-level permissions blocks.

