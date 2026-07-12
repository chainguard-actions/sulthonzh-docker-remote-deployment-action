<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.52

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.52** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised.

ci.yml: actions/checkout@v4 (5 occurrences)
code-review.yml: actions/checkout@v6, sulthonzh/code-reviewer@main (multiple occurrences)
release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/release.yml:18`

### missing-permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level 'permissions:' block. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three workflow files:

**ci.yml**: Added top-level `permissions: {}` (deny-all) and per-job `permissions: { contents: read }` for all 5 jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image). Pinned all 5 `actions/checkout@v4` references to SHA `34e114876b0b11c390a56381ad16ebd13914f8d5`.

**code-review.yml**: Pinned `actions/checkout@v6` (×6) to SHA `df4cb1c069e1874edd31b4311f1884172cec0e10` and `sulthonzh/code-reviewer@main` (×7) to SHA `4546aa400043740f47f0023b54663fdb592b253f`. This file already had a permissions block so no change needed there.

**release.yml**: Pinned `actions/checkout@v4` (×2) to `34e114876b0b11c390a56381ad16ebd13914f8d5`, `docker/setup-qemu-action@v3` to `c7c53464625b32c7a7e944ae62b3e17d2b600130`, `docker/setup-buildx-action@v3` to `8d2750c68a42422c14e847fe6c8ac0403b4cbd6f`, `docker/login-action@v3` (×2) to `c94ce9fb468520275223c153574b00df6fe4bcc9`, `docker/metadata-action@v5` to `c299e40c65443455700f0fdfc63efafe5b349051`, `docker/build-push-action@v5` to `ca052bb54ab0790a636c9b5f226502c73d547a25`, and `softprops/action-gh-release@v1` to `de2c0eb89ae2a093876385947365aca7b0e5f844`. This file already had job-level permissions blocks.

