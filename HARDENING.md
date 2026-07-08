<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.42

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.42** was hardened automatically. 2 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use action references pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks. ci.yml: actions/checkout@v4 (×5). code-review.yml: actions/checkout@v6 (×3), sulthonzh/code-reviewer@main (×7, branch ref). release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:44`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:32`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:49`
- `.github/workflows/code-review.yml:62`
- `.github/workflows/code-review.yml:78`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:102`
- `.github/workflows/code-review.yml:109`
- `.github/workflows/code-review.yml:114`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:34`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:68`
- `.github/workflows/release.yml:80`

### missing-permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level 'permissions:' block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by replacing mutable tags/branch refs with full 40-character commit SHAs (with tag comments for readability). Specifically: actions/checkout@v4→SHA (ci.yml ×5, release.yml ×2), actions/checkout@v6→SHA (code-review.yml ×5), sulthonzh/code-reviewer@main→SHA (code-review.yml ×7), docker/setup-qemu-action@v3→SHA, docker/setup-buildx-action@v3→SHA, docker/login-action@v3→SHA (×2), docker/metadata-action@v5→SHA, docker/build-push-action@v5→SHA, softprops/action-gh-release@v1→SHA. Added top-level `permissions: contents: read` to ci.yml to address the missing-permissions finding (minimum permissions needed for checkout-only jobs).

### Iteration 2

**Fixes applied:** hardcoded-credentials, github-env-injection

**Notes:**

1. docker-compose.yml line 46: Replaced hardcoded `DB_PASSWORD: mypassword` with `DB_PASSWORD: ${DB_PASSWORD}` so the password is injected from the runtime environment rather than stored in plaintext in the file. 2. .github/workflows/release.yml lines 60-65: Sanitized the CHANGELOG variable (derived from attacker-controlled git commit messages) before writing to $GITHUB_OUTPUT. Added a sanitization step using `printf '%s' "$CHANGELOG" | tr -d '\r' | sed 's/EOF/E0F/g'` to strip carriage returns and prevent heredoc delimiter injection. Also changed the heredoc delimiter from `EOF` to `CHANGELOG_DELIMITER` to further reduce collision risk, and quoted `$GITHUB_OUTPUT` references.

### Iteration 3

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection vulnerabilities in .github/workflows/release.yml:
1. Line 77 (TAG): Added SAFE_TAG=$(printf '%s' "$TAG" | tr -d '\n\r') to strip newlines before writing to GITHUB_OUTPUT, preventing newline injection from GITHUB_REF.
2. Line 89 (SAFE_CHANGELOG): Changed tr -d '\r' to tr -d '\r\n' to strip both carriage returns and newlines from the changelog content, preventing newline injection from attacker-controlled commit messages. Also added sed substitution to neutralize the heredoc delimiter string (CHANGELOG_DELIMITER) if it appears in commit messages, preventing premature heredoc termination.

