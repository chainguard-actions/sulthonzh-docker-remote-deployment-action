<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.47

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.47** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or branch names instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks where a tag or branch can be silently updated to point to malicious code.

**.github/workflows/ci.yml** — all uses are unpinned tags:
- `actions/checkout@v4` (×5)

**.github/workflows/code-review.yml** — all uses are unpinned tags or branch refs:
- `actions/checkout@v6` (×5)
- `sulthonzh/code-reviewer@main` (×7, using a mutable branch `main`)

**.github/workflows/release.yml** — all uses are unpinned tags:
- `actions/checkout@v4` (×2)
- `docker/setup-qemu-action@v3`
- `docker/setup-buildx-action@v3`
- `docker/login-action@v3` (×2)
- `docker/metadata-action@v5`
- `docker/build-push-action@v5`
- `softprops/action-gh-release@v1`

All references must be replaced with full 40-character hex commit SHAs (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:23`
- `.github/workflows/code-review.yml:33`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:72`
- `.github/workflows/release.yml:96`

### missing-permissions (severity: medium)

`.github/workflows/ci.yml` has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). A minimal `permissions: read-all` or specific per-job scopes should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by replacing mutable tags/branch refs with full 40-character commit SHAs (resolved via lookup_action_sha). Added a top-level `permissions: contents: read` block to ci.yml which had no permissions defined. Pinned actions: actions/checkout@v4 → SHA 11d5960..., actions/checkout@v6 → SHA d23441a..., sulthonzh/code-reviewer@main → SHA d882af6..., docker/setup-qemu-action@v3 → SHA c7c5346..., docker/setup-buildx-action@v3 → SHA 8d2750c..., docker/login-action@v3 → SHA c94ce9f..., docker/metadata-action@v5 → SHA c299e40..., docker/build-push-action@v5 → SHA ca052bb..., softprops/action-gh-release@v1 → SHA de2c0eb...

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in .github/workflows/release.yml at line 77. The TAG variable (derived from GITHUB_REF, which is user-controlled via the pushed tag name) was being written directly to $GITHUB_OUTPUT without sanitization. Fixed by introducing a `safe_tag` variable that strips newline and carriage-return characters using `printf '%s' "$TAG" | tr -d '\n\r'`, then writing `safe_tag` to $GITHUB_OUTPUT instead of the raw TAG value. Also added quoting around $GITHUB_OUTPUT for best practice.

