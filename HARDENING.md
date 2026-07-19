<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.42

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.42** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all three workflow files use mutable tag or branch refs instead of pinned 40-character SHA digests, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved.

Failing references in ci.yml: actions/checkout@v4 (x5).

Failing references in code-review.yml: actions/checkout@v6 (x3), sulthonzh/code-reviewer@main (x6, branch ref — especially risky).

Failing references in release.yml: actions/checkout@v4 (x2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:40`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:56`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:31`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:47`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:72`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository default token permissions, which may be overly broad (e.g. write access to contents and packages).

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references across ci.yml, code-review.yml, and release.yml by replacing mutable tag/branch refs with full 40-character SHA digests (preserving the original tag in a comment). Added a top-level `permissions: contents: read` block to ci.yml to address the missing-permissions finding. All SHAs were resolved via lookup_action_sha: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6→df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main→d882af6cd1ae55f692c0a8dfc6ff464115ccf89c, docker/setup-qemu-action@v3→c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3→8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3→c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5→c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5→ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1→de2c0eb89ae2a093876385947365aca7b0e5f844.

