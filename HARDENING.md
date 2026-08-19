<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks.

.github/workflows/code-review.yml:
  - uses: actions/checkout@v6  (tag)
  - uses: sulthonzh/code-reviewer@main  (branch — appears 8 times)

.github/workflows/main.yml:
  - uses: actions/checkout@v6  (tag)
  - uses: docker/setup-qemu-action@v4  (tag)
  - uses: docker/setup-buildx-action@v4  (tag)
  - uses: docker/login-action@v4  (tag)
  - uses: docker/build-push-action@v7  (tag)

All of these should be pinned to a full 40-character hex commit SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:65`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:77`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:29`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/main.yml` has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the default repository token permissions, which may be broader than necessary (e.g. write access to contents). A minimal `permissions:` block should be added at the top level or on each job.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references in both workflow files by resolving each tag/branch to its full 40-character commit SHA using lookup_action_sha. Preserved original tag/branch names as inline comments for readability. Added a minimal `permissions: contents: read` block at the top level of main.yml to satisfy the missing-permissions finding. The code-review.yml already had a permissions block so no change was needed there.

