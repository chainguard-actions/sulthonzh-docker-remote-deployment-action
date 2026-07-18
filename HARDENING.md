<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.48

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.48** was hardened automatically. 2 finding(s) were identified and resolved across 4 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all three workflow files use mutable tag or branch refs instead of immutable 40-character SHA digests, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (×5)
code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×7)
release.yml: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

All should be pinned to full 40-character commit SHAs, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:44`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:33`
- `.github/workflows/code-review.yml:37`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:62`
- `.github/workflows/code-review.yml:66`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:85`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:68`
- `.github/workflows/release.yml:80`
- `.github/workflows/release.yml:101`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` block and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be broader than necessary. A top-level `permissions: {}` or per-job minimal permissions block should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references across ci.yml, code-review.yml, and release.yml by pinning each to its full 40-character commit SHA (with the original tag preserved as a comment). Added `permissions: {}` top-level block to ci.yml to address the missing-permissions finding. SHAs resolved: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6→df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main→d882af6cd1ae55f692c0a8dfc6ff464115ccf89c, docker/setup-qemu-action@v3→c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3→8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3→c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5→c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5→ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1→de2c0eb89ae2a093876385947365aca7b0e5f844.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in .github/workflows/release.yml at line 57. The TAG variable (derived from GITHUB_REF) is now sanitized with `printf '%s' "$TAG" | tr -d '\n\r'` before being written to $GITHUB_OUTPUT. The sanitized value is stored in `safe_tag` and used in the echo statement instead of the raw TAG value.

### Iteration 3

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in .github/workflows/release.yml (line 72) in the 'Generate changelog' step. The static 'EOF' heredoc delimiter used when writing $CHANGELOG to $GITHUB_OUTPUT was replaced with a cryptographically random delimiter: `DELIM="EOF_$(openssl rand -hex 16)"`. This prevents an attacker from crafting a commit message containing 'EOF' on its own line to escape the heredoc and inject arbitrary key=value pairs into the GitHub Actions output context. The multi-line changelog content is preserved while the injection vector is eliminated.

### Iteration 1

**Fixes applied:** hardcoded-credentials, github-env-injection

**Notes:**

1. docker-compose.yml: Replaced hardcoded 'changeme' passwords at lines 24 and 46 with Docker Compose environment variable interpolation (${POSTGRES_PASSWORD} and ${DB_PASSWORD}), so credentials must be supplied at runtime via environment variables or a .env file rather than being committed to the repository.

2. .github/workflows/release.yml: Sanitized the $CHANGELOG variable (derived from attacker-controllable git commit messages) before writing to $GITHUB_OUTPUT. Added `safe_changelog=$(printf '%s' "$CHANGELOG" | tr -d '\r' | tr '\n' ' ')` to strip all newlines, preventing injection of arbitrary key=value pairs into GITHUB_OUTPUT via crafted commit messages. The sanitized value is then written using the existing randomized heredoc delimiter approach.

