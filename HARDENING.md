<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.40

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.40** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all three workflow files are pinned to mutable tags or branch names rather than immutable 40-character commit SHAs. This exposes the workflows to supply-chain attacks if any referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (×5)
code-review.yml: actions/checkout@v6 (×4), sulthonzh/code-reviewer@main (×9)
release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, actions/checkout@v4, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:25`
- `.github/workflows/code-review.yml:42`
- `.github/workflows/code-review.yml:48`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:78`
- `.github/workflows/code-review.yml:84`
- `.github/workflows/code-review.yml:92`
- `.github/workflows/code-review.yml:98`
- `.github/workflows/code-review.yml:104`
- `.github/workflows/code-review.yml:110`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:62`

### missing-permissions (severity: medium)

The CI workflow file has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the default token permissions, which may be overly broad (write access to contents and packages in many repository configurations).

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three workflow files:

**ci.yml**:
- Added top-level `permissions: contents: read` block to address missing-permissions finding
- Pinned all 5 `actions/checkout@v4` references to SHA `34e114876b0b11c390a56381ad16ebd13914f8d5`

**code-review.yml** (already had permissions block, only unpinned-uses fixed):
- Pinned 4 `actions/checkout@v6` references to SHA `df4cb1c069e1874edd31b4311f1884172cec0e10`
- Pinned 9 `sulthonzh/code-reviewer@main` references to SHA `d882af6cd1ae55f692c0a8dfc6ff464115ccf89c`

**release.yml** (already had job-level permissions, only unpinned-uses fixed):
- Pinned `actions/checkout@v4` (×2) to SHA `34e114876b0b11c390a56381ad16ebd13914f8d5`
- Pinned `docker/setup-qemu-action@v3` to SHA `c7c53464625b32c7a7e944ae62b3e17d2b600130`
- Pinned `docker/setup-buildx-action@v3` to SHA `8d2750c68a42422c14e847fe6c8ac0403b4cbd6f`
- Pinned `docker/login-action@v3` (×2) to SHA `c94ce9fb468520275223c153574b00df6fe4bcc9`
- Pinned `docker/metadata-action@v5` to SHA `c299e40c65443455700f0fdfc63efafe5b349051`
- Pinned `docker/build-push-action@v5` to SHA `ca052bb54ab0790a636c9b5f226502c73d547a25`
- Pinned `softprops/action-gh-release@v1` to SHA `de2c0eb89ae2a093876385947365aca7b0e5f844`

All original tags are preserved as inline comments (e.g., `# v4`) for readability.

