<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.24

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.24** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tags or branch names instead of pinned 40-character SHA digests, making the workflows vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/main.yml:
- `actions/checkout@v6` (line 14)
- `docker/setup-qemu-action@v4` (line 17)
- `docker/setup-buildx-action@v4` (line 20)
- `docker/login-action@v4` (line 23)
- `docker/build-push-action@v7` (line 29)

.github/workflows/code-review.yml:
- `actions/checkout@v6` (lines 28, 43, 68, 80, 91)
- `sulthonzh/code-reviewer@main` (lines 31, 46, 57, 71, 75, 83, 93)

All of these should be pinned to a full 40-character commit SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:29`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:71`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:93`

### missing-permissions (severity: medium)

`.github/workflows/main.yml` has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without an explicit permissions block, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., `write` on all scopes for private repositories). A minimal permissions block such as `permissions: contents: read` should be added.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references in both workflow files by resolving each tag to its full 40-character SHA digest (preserving the original tag as a comment). Specifically: actions/checkout@v6 → d23441a48e516b6c34aea4fa41551a30e30af803, docker/setup-qemu-action@v4 → 96fe6ef7f33517b61c61be40b68a1882f3264fb8, docker/setup-buildx-action@v4 → bb05f3f5519dd87d3ba754cc423b652a5edd6d2c, docker/login-action@v4 → af1e73f918a031802d376d3c8bbc3fe56130a9b0, docker/build-push-action@v7 → 53b7df96c91f9c12dcc8a07bcb9ccacbed38856a, sulthonzh/code-reviewer@main → d882af6cd1ae55f692c0a8dfc6ff464115ccf89c. Added a top-level `permissions: contents: read` block to main.yml to address the missing-permissions finding. code-review.yml already had an explicit permissions block.

