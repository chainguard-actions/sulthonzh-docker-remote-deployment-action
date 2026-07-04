<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action--/v1.4.36

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action--/v1.4.36** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (used in 5 steps)
code-review.yml: actions/checkout@v6, sulthonzh/code-reviewer@main (used in 6 steps)
release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and none of its jobs define job-level permissions. Without explicit permissions, the workflow inherits the default repository token permissions, which may be broader than necessary.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by replacing mutable tag/branch refs with full 40-character SHA commits (with tag comments for readability). Added `permissions: {}` top-level block to ci.yml which had no permissions defined. Specific changes: ci.yml - pinned 5x actions/checkout@v4 to SHA, added permissions: {}; code-review.yml - pinned 6x actions/checkout@v6 and 6x sulthonzh/code-reviewer@main to their respective SHAs; release.yml - pinned actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, and softprops/action-gh-release@v1 to their respective SHAs.

