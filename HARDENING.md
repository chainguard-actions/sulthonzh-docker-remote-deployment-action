<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.10** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in .github/workflows/main.yml use mutable tag refs instead of pinned 40-character SHA commits, making the workflow vulnerable to supply-chain attacks if those tags are moved. Failing references: `actions/checkout@v6` (line 14), `docker/setup-qemu-action@v4` (line 17), `docker/setup-buildx-action@v4` (line 20), `docker/login-action@v4` (line 23), `docker/build-push-action@v7` (line 30).

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:30`

### unpinned-uses (severity: high)

All `uses:` references in .github/workflows/code-review.yml use mutable tag or branch refs instead of pinned 40-character SHA commits. Notably, `sulthonzh/code-reviewer@main` (a branch ref) is used repeatedly, which is especially dangerous as any push to `main` of that external repo immediately affects this workflow. Failing references: `actions/checkout@v6` (lines 27, 40, 65, 82, 92), `sulthonzh/code-reviewer@main` (lines 31, 44, 55, 68, 73, 84, 95).

Locations:

- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:65`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:84`
- `.github/workflows/code-review.yml:92`
- `.github/workflows/code-review.yml:95`

### missing-permissions (severity: medium)

.github/workflows/main.yml has no top-level `permissions:` key and no job-level `permissions:` key on its only job (`build`). Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes). A minimal permissions block should be added (e.g., `contents: read` for a build/push workflow).

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:

1. **unpinned-uses (main.yml)**: Pinned all 5 `uses:` references to full 40-character SHA commits:
   - `actions/checkout@v6` → `@df4cb1c069e1874edd31b4311f1884172cec0e10 # v6`
   - `docker/setup-qemu-action@v4` → `@96fe6ef7f33517b61c61be40b68a1882f3264fb8 # v4`
   - `docker/setup-buildx-action@v4` → `@bb05f3f5519dd87d3ba754cc423b652a5edd6d2c # v4`
   - `docker/login-action@v4` → `@af1e73f918a031802d376d3c8bbc3fe56130a9b0 # v4`
   - `docker/build-push-action@v7` → `@53b7df96c91f9c12dcc8a07bcb9ccacbed38856a # v7`

2. **unpinned-uses (code-review.yml)**: Pinned all 12 `uses:` references:
   - All 5 `actions/checkout@v6` → `@df4cb1c069e1874edd31b4311f1884172cec0e10 # v6`
   - All 7 `sulthonzh/code-reviewer@main` → `@d882af6cd1ae55f692c0a8dfc6ff464115ccf89c # main`

3. **missing-permissions (main.yml)**: Added top-level `permissions: contents: read` block. The workflow only needs to read the repository to build and push to DockerHub (which uses secrets, not GITHUB_TOKEN write access).

