<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.46

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.46** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/ci.yml: actions/checkout@v4 (×5 occurrences)
.github/workflows/code-review.yml: actions/checkout@v6 (×2), sulthonzh/code-reviewer@main (×6 occurrences)
.github/workflows/release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:37`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:78`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:110`
- `.github/workflows/code-review.yml:117`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:29`
- `.github/workflows/release.yml:35`
- `.github/workflows/release.yml:44`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:84`

### missing-permissions (severity: medium)

The CI workflow file has no top-level 'permissions:' key and none of its jobs define job-level permissions. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. Explicit minimal permissions should be declared for each job.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable TAG is derived from GITHUB_REF (a github.* context value) and written directly to $GITHUB_OUTPUT without sanitization (no 'printf "%s" | tr -d newlines' step). A tag name containing newline or carriage-return characters could inject additional key=value pairs into GITHUB_OUTPUT. The offending line is: echo "tag=$TAG" >> $GITHUB_OUTPUT

Locations:

- `.github/workflows/release.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. unpinned-uses: Pinned all action references to full 40-character commit SHAs in ci.yml, code-review.yml, and release.yml:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 # v6
   - sulthonzh/code-reviewer@main → @4546aa400043740f47f0023b54663fdb592b253f # main
   - docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130 # v3
   - docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3
   - docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 # v3
   - docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051 # v5
   - docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25 # v5
   - softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844 # v1

2. missing-permissions: Added top-level 'permissions: {}' to ci.yml and per-job 'permissions: contents: read' for each job that needs to check out code.

3. github-env-injection: Fixed the 'Generate changelog' step in release.yml by sanitizing the TAG variable derived from GITHUB_REF before writing to $GITHUB_OUTPUT using 'printf "%s" | tr -d newlines' pattern.

### Iteration 2

**Fixes applied:** hardcoded-credentials, broad-permissions

**Notes:**

1. docker-compose.yml: Replaced hardcoded literal passwords 'changeme' on lines 24 and 44 with Docker Compose variable interpolation (${POSTGRES_PASSWORD} and ${DB_PASSWORD}). These values must now be supplied via the host environment or a .env file — they are never stored in the file itself. 2. .github/workflows/code-review.yml: Removed the broad top-level permissions block (pull-requests:write, contents:write, checks:write, statuses:write) and replaced it with 'permissions: {}' at the workflow level. Added minimal per-job permissions: secret-scan (contents:read, checks:write), ai-review (contents:read, pull-requests:write), quality-gate (contents:read, checks:write, statuses:write, pull-requests:write), auto-merge (contents:write, pull-requests:write), auto-release (contents:write). This follows the principle of least privilege and limits the blast radius of any compromise.

