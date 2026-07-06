<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action--/v1.4.39

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action--/v1.4.39** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (used 5 times)

release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

code-review.yml: actions/checkout@v6 (multiple steps), sulthonzh/code-reviewer@main (multiple steps — @main is a mutable branch ref)

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:34`
- `.github/workflows/ci.yml:44`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:42`
- `.github/workflows/release.yml:56`
- `.github/workflows/release.yml:68`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:90`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:104`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define job-level permissions. This means the workflow runs with the default GitHub token permissions, which may be broader than necessary.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

1. ci.yml: Added top-level 'permissions: contents: read' block. Pinned all 5 occurrences of actions/checkout@v4 to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5.
2. release.yml: Pinned actions/checkout@v4 → 34e114876b0b11c390a56381ad16ebd13914f8d5, docker/setup-qemu-action@v3 → c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → 8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 (x2) → c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 → c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → de2c0eb89ae2a093876385947365aca7b0e5f844.
3. code-review.yml: Pinned all occurrences of actions/checkout@v6 → df4cb1c069e1874edd31b4311f1884172cec0e10 and sulthonzh/code-reviewer@main → d0c6f9c936438fbd487b575f55f739ba52f4cc37. All mutable tag/branch refs replaced with full 40-character commit SHAs with tag comments for readability.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed both github-env-injection issues in .github/workflows/release.yml 'Generate changelog' step:
1. TAG value: Added `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` sanitization before writing `echo "tag=$safe_tag" >> "$GITHUB_OUTPUT"`.
2. CHANGELOG value: Added `safe_changelog=$(printf '%s' "$CHANGELOG" | tr -d '\r' | sed 's/^EOF$/[EOF]/')` to strip carriage returns and neutralize any line that equals the heredoc delimiter. Changed the heredoc delimiter from 'EOF' to 'CHANGELOG_DELIM' to reduce collision risk with commit message content. Used `printf '%s\n'` instead of `echo` for safer output writing.

