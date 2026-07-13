<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.54

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.54** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All uses: references across all three workflow files use mutable tags or branch names instead of full 40-character SHA commit digests, making them vulnerable to supply-chain attacks. Failing references include: ci.yml: actions/checkout@v4 (×5); code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×8); release.yml: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:25`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:56`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:29`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:68`
- `.github/workflows/release.yml:84`
- `.github/workflows/release.yml:107`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. Without explicit permissions, the workflow inherits the default repository token permissions, which may be overly broad.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references across ci.yml, code-review.yml, and release.yml by replacing mutable tags/branch names with full 40-character SHA digests (preserving original tags as comments). Added a top-level `permissions: contents: read` block to ci.yml to address the missing-permissions finding. All SHAs were resolved using lookup_action_sha: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6→df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main→4546aa400043740f47f0023b54663fdb592b253f, docker/setup-qemu-action@v3→c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3→8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3→c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5→c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5→ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1→de2c0eb89ae2a093876385947365aca7b0e5f844.

