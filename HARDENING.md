<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.53

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.53** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

ci.yml uses tag-based (non-SHA) refs for all action steps: `actions/checkout@v4` (×5). These should be pinned to full 40-character commit SHAs to prevent supply-chain attacks.

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:48`

### unpinned-uses (severity: high)

code-review.yml uses tag-based and branch-based (non-SHA) refs: `actions/checkout@v6` (×multiple) and `sulthonzh/code-reviewer@main` (×multiple). Using `@main` is especially dangerous as it tracks a mutable branch. All should be pinned to full 40-character commit SHAs.

Locations:

- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:34`
- `.github/workflows/code-review.yml:42`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:95`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:110`

### unpinned-uses (severity: high)

release.yml uses tag-based (non-SHA) refs for all action steps: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, and `softprops/action-gh-release@v1`. All should be pinned to full 40-character commit SHAs.

Locations:

- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:31`
- `.github/workflows/release.yml:37`
- `.github/workflows/release.yml:47`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:76`

### permissions (severity: medium)

missing-permissions: ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions

**Notes:**

Fixed all findings across three workflow files:

1. **ci.yml** (unpinned-uses + missing-permissions):
   - Pinned all 5 `actions/checkout@v4` to SHA `11d5960a326750d5838078e36cf38b85af677262 # v4`
   - Added top-level `permissions: {}` block to enforce least-privilege (workflow only runs linting/build checks, needs no GitHub API access)

2. **code-review.yml** (unpinned-uses):
   - Pinned all 5 `actions/checkout@v6` to SHA `d23441a48e516b6c34aea4fa41551a30e30af803 # v6`
   - Pinned all 6 `sulthonzh/code-reviewer@main` to SHA `d882af6cd1ae55f692c0a8dfc6ff464115ccf89c # main`
   - The file already had a `permissions:` block so no change needed there

3. **release.yml** (unpinned-uses):
   - Pinned `actions/checkout@v4` → `11d5960a326750d5838078e36cf38b85af677262 # v4` (×2)
   - Pinned `docker/setup-qemu-action@v3` → `c7c53464625b32c7a7e944ae62b3e17d2b600130 # v3`
   - Pinned `docker/setup-buildx-action@v3` → `8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3`
   - Pinned `docker/login-action@v3` → `c94ce9fb468520275223c153574b00df6fe4bcc9 # v3` (×2)
   - Pinned `docker/metadata-action@v5` → `c299e40c65443455700f0fdfc63efafe5b349051 # v5`
   - Pinned `docker/build-push-action@v5` → `ca052bb54ab0790a636c9b5f226502c73d547a25 # v5`
   - Pinned `softprops/action-gh-release@v1` → `de2c0eb89ae2a093876385947365aca7b0e5f844 # v1`
   - The file already had job-level `permissions:` blocks so no change needed there

