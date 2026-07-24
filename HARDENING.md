<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.61

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.61** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions by mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved.

.github/workflows/ci.yml:
- uses: actions/checkout@v4 (appears 5 times)

.github/workflows/release.yml:
- uses: actions/checkout@v4
- uses: docker/setup-qemu-action@v3
- uses: docker/setup-buildx-action@v3
- uses: docker/login-action@v3 (×2)
- uses: docker/metadata-action@v5
- uses: docker/build-push-action@v5
- uses: softprops/action-gh-release@v1

.github/workflows/code-review.yml:
- uses: actions/checkout@v6 (×5)
- uses: sulthonzh/code-reviewer@main (×7, branch ref)

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/release.yml:12`
- `.github/workflows/code-review.yml:21`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the default repository token permissions, which may be broader than necessary (e.g., write access to contents). This is especially risky because the workflow triggers on `pull_request`, meaning untrusted code from forks could run with elevated permissions.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three workflow files:

1. ci.yml: Added top-level `permissions: contents: read` block (missing-permissions fix). Pinned all 5 occurrences of `actions/checkout@v4` to SHA `11d5960a326750d5838078e36cf38b85af677262`.

2. release.yml: Pinned all 8 action references to full SHAs — actions/checkout@v4 → 11d5960a..., docker/setup-qemu-action@v3 → c7c53464..., docker/setup-buildx-action@v3 → 8d2750c6..., docker/login-action@v3 (×2) → c94ce9fb..., docker/metadata-action@v5 → c299e40c..., docker/build-push-action@v5 → ca052bb5..., softprops/action-gh-release@v1 → de2c0eb8...

3. code-review.yml: Pinned all 5 occurrences of actions/checkout@v6 → d23441a4... and all 7 occurrences of sulthonzh/code-reviewer@main → d882af6c... All original tags/branch names preserved as inline comments.

