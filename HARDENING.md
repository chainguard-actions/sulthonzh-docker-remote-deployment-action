<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.23

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.23** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in main.yml are pinned to mutable version tags instead of immutable 40-character SHA digests, making the workflow vulnerable to supply-chain attacks if the referenced tags are moved or compromised. Failing references: `actions/checkout@v6` (line 14), `docker/setup-qemu-action@v4` (line 17), `docker/setup-buildx-action@v4` (line 20), `docker/login-action@v4` (line 23), `docker/build-push-action@v7` (line 30).

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:30`

### unpinned-uses (severity: high)

All `uses:` references in code-review.yml are pinned to mutable version tags or branch names instead of immutable 40-character SHA digests. Failing references include: `actions/checkout@v6` (multiple steps) and `sulthonzh/code-reviewer@main` (multiple steps — the `@main` branch reference is especially dangerous as it tracks a moving branch head).

Locations:

- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:33`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:56`
- `.github/workflows/code-review.yml:62`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:92`

### missing-permissions (severity: medium)

The workflow file main.yml has no top-level `permissions:` key and the single `build` job also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes by default in many repositories). A minimal permissions block should be added (e.g., `permissions: contents: read`).

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:
1. main.yml (unpinned-uses): Pinned all 5 `uses:` references to immutable 40-char SHAs — actions/checkout@df4cb1c069e1874edd31b4311f1884172cec0e10 (v6), docker/setup-qemu-action@96fe6ef7f33517b61c61be40b68a1882f3264fb8 (v4), docker/setup-buildx-action@bb05f3f5519dd87d3ba754cc423b652a5edd6d2c (v4), docker/login-action@af1e73f918a031802d376d3c8bbc3fe56130a9b0 (v4), docker/build-push-action@53b7df96c91f9c12dcc8a07bcb9ccacbed38856a (v7). Original tag preserved as inline comment.
2. code-review.yml (unpinned-uses): Pinned all 12 `uses:` references — actions/checkout@df4cb1c069e1874edd31b4311f1884172cec0e10 (v6) and sulthonzh/code-reviewer@d882af6cd1ae55f692c0a8dfc6ff464115ccf89c (main) across all jobs.
3. main.yml (missing-permissions): Added top-level `permissions: contents: read` block — the minimum required for checkout and Docker build/push operations. code-review.yml already had an explicit permissions block.

