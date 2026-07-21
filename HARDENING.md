<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.65

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.65** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference external actions using mutable tag or branch refs instead of pinned 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks where a tag is silently moved to a malicious commit.

- ci.yml: `actions/checkout@v4` (used in all 5 jobs)
- code-review.yml: `actions/checkout@v6` (used in multiple jobs), `sulthonzh/code-reviewer@main` (used in 8 steps — notably pinned to a mutable branch `main`)
- release.yml: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, `softprops/action-gh-release@v1`

All should be replaced with full SHA refs, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:42`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:23`
- `.github/workflows/code-review.yml:34`
- `.github/workflows/code-review.yml:39`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:85`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:47`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:79`
- `.github/workflows/release.yml:97`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and none of its 5 jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). A top-level `permissions: {}` or minimal per-job permissions should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across ci.yml, code-review.yml, and release.yml by replacing mutable tag/branch refs with full 40-character SHA commit hashes (with original tag preserved as a comment). Added top-level `permissions: {}` to ci.yml to enforce least-privilege token access. All SHAs were resolved via lookup_action_sha: actions/checkout@v4→11d5960a, actions/checkout@v6→d23441a4, sulthonzh/code-reviewer@main→d882af6c, docker/setup-qemu-action@v3→c7c53464, docker/setup-buildx-action@v3→8d2750c6, docker/login-action@v3→c94ce9fb, docker/metadata-action@v5→c299e40c, docker/build-push-action@v5→ca052bb5, softprops/action-gh-release@v1→de2c0eb8.

