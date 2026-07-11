<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.48

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.48** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files use mutable tag or branch refs instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved. ci.yml uses actions/checkout@v4 (5 times). code-review.yml uses actions/checkout@v6 (multiple times) and sulthonzh/code-reviewer@main (multiple times). release.yml uses actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (twice), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:34`
- `.github/workflows/ci.yml:44`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:45`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:76`
- `.github/workflows/code-review.yml:84`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/code-review.yml:108`
- `.github/workflows/code-review.yml:116`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:68`
- `.github/workflows/release.yml:88`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and no per-job permissions: keys on any of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image). This means the workflow runs with the default token permissions, which may be broader than necessary.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by replacing mutable tags/branches with full 40-character commit SHAs (with tag comments for readability): actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main → @4546aa400043740f47f0023b54663fdb592b253f, docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844. Added top-level 'permissions: contents: read' block to ci.yml to address the missing-permissions finding.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection issues in .github/workflows/release.yml 'Generate changelog' step:
1. $TAG (line 77): Added sanitization via `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT.
2. $CHANGELOG (lines 84-87): Added sanitization via `safe_changelog=$(printf '%s' "$CHANGELOG" | tr -d '\r')` before writing via heredoc to $GITHUB_OUTPUT. Newlines are preserved (required for multiline heredoc output) while carriage returns are stripped to prevent \r\n sequences from injecting a bare EOF line that would terminate the heredoc and allow additional key=value injection. Also quoted $GITHUB_OUTPUT references for robustness.

