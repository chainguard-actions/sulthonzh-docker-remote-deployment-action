<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.52

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.52** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced action is compromised or its tag is moved.

- ci.yml: `actions/checkout@v4` (used in all 5 jobs)
- code-review.yml: `actions/checkout@v6`, `sulthonzh/code-reviewer@main` (used in 8 steps)
- release.yml: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, `softprops/action-gh-release@v1`

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. write access to contents and packages). A minimal `permissions: {}` or specific scopes such as `contents: read` should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by resolving each tag/branch to its full 40-character commit SHA (preserving the original tag as a comment). Added a top-level `permissions: contents: read` block to ci.yml to address the missing-permissions finding. The code-review.yml already had a permissions block so only its action references were updated. The release.yml already had job-level permissions blocks so only its action references were updated.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed both unsanitized writes to $GITHUB_OUTPUT in the 'Generate changelog' step of .github/workflows/release.yml:
1. TAG: Added `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` and used `$safe_tag` when writing `tag=` to $GITHUB_OUTPUT.
2. CHANGELOG: Added `safe_changelog=$(printf '%s' "$CHANGELOG" | tr -d '\r')` and used `$safe_changelog` in the heredoc write to $GITHUB_OUTPUT. Carriage returns are stripped to prevent injection; legitimate newlines are preserved since the heredoc EOF delimiter terminates the value. Also added quotes around $GITHUB_OUTPUT references and the git log range argument for correctness.

