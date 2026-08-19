<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.8** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in main.yml are pinned to mutable version tags rather than immutable 40-character SHA digests, making the workflow vulnerable to supply-chain attacks if the upstream action is compromised or the tag is moved. Failing references: `actions/checkout@v6`, `docker/setup-qemu-action@v4`, `docker/setup-buildx-action@v4`, `docker/login-action@v4`, `docker/build-push-action@v7`.

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:21`
- `.github/workflows/main.yml:25`
- `.github/workflows/main.yml:31`

### unpinned-uses (severity: high)

All `uses:` references in code-review.yml are pinned to mutable version tags or branch names rather than immutable 40-character SHA digests. Failing references: `actions/checkout@v6` (used multiple times) and `sulthonzh/code-reviewer@main` (used multiple times — `@main` is a branch reference and is especially dangerous as it tracks the tip of the branch).

Locations:

- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/code-review.yml:42`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:85`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:101`
- `.github/workflows/code-review.yml:110`
- `.github/workflows/code-review.yml:116`
- `.github/workflows/code-review.yml:124`
- `.github/workflows/code-review.yml:130`

### missing-permissions (severity: medium)

The workflow file main.yml has no top-level `permissions:` key and the single `build` job also has no `permissions:` key. Without explicit permissions, the workflow runs with the default token permissions (which may be read/write depending on repository settings), violating the principle of least privilege.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:
1. main.yml - pinned all 5 `uses:` references to full SHA digests: actions/checkout@df4cb1c069e1874edd31b4311f1884172cec0e10 (v6), docker/setup-qemu-action@96fe6ef7f33517b61c61be40b68a1882f3264fb8 (v4), docker/setup-buildx-action@bb05f3f5519dd87d3ba754cc423b652a5edd6d2c (v4), docker/login-action@af1e73f918a031802d376d3c8bbc3fe56130a9b0 (v4), docker/build-push-action@53b7df96c91f9c12dcc8a07bcb9ccacbed38856a (v7). Added top-level `permissions: contents: read` block.
2. code-review.yml - pinned all 13 `uses:` references: actions/checkout@df4cb1c069e1874edd31b4311f1884172cec0e10 (v6, used 5 times) and sulthonzh/code-reviewer@d882af6cd1ae55f692c0a8dfc6ff464115ccf89c (main, used 8 times). The file already had a permissions block so no change was needed there.

