<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.45

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.45** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved.

.github/workflows/ci.yml: actions/checkout@v4 (lines 13, 19, 25, 33, 43)

.github/workflows/code-review.yml: actions/checkout@v6 (lines 27, 40, 67, 84, 95), sulthonzh/code-reviewer@main (lines 30, 43, 56, 70, 75, 87, 97)

.github/workflows/release.yml: actions/checkout@v4 (lines 18, 62), docker/setup-qemu-action@v3 (line 21), docker/setup-buildx-action@v3 (line 24), docker/login-action@v3 (lines 27, 33), docker/metadata-action@v5 (line 38), docker/build-push-action@v5 (line 49), softprops/action-gh-release@v1 (line 80)

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:49`
- `.github/workflows/release.yml:80`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. This means the GITHUB_TOKEN is granted its default (broad) permissions for every job in the workflow.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes two unsanitized values to $GITHUB_OUTPUT without applying the required `printf '%s' ... | tr -d '\n\r'` sanitization:

1. Line 69: `echo "tag=$TAG" >> $GITHUB_OUTPUT` — TAG is derived from `${GITHUB_REF#refs/tags/}`, a GitHub context value. A tag name containing newline characters could inject additional key=value pairs into GITHUB_OUTPUT.

2. Line 78: `echo "$CHANGELOG" >> $GITHUB_OUTPUT` — CHANGELOG is populated from `git log` commit messages, which are attacker-controlled (any contributor can craft a commit message with newlines). Writing this directly to GITHUB_OUTPUT without sanitization allows injection of arbitrary output variables.

Locations:

- `.github/workflows/release.yml:69`
- `.github/workflows/release.yml:78`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. unpinned-uses: Pinned all action references to full 40-char commit SHAs with tag comments:
   - ci.yml: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (5 occurrences)
   - code-review.yml: actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 (5 occurrences), sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c (7 occurrences)
   - release.yml: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 (2 occurrences), docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 (2 occurrences), docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844

2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml (the minimum needed for checkout-only jobs).

3. github-env-injection: Fixed the 'Generate changelog' step in release.yml:
   - TAG is now sanitized with `printf '%s' "$RAW_TAG" | tr -d '\n\r'` before writing to GITHUB_OUTPUT
   - CHANGELOG is now written using a randomized heredoc delimiter (the safe multi-line format per GitHub docs), replacing the unsafe direct `echo "$CHANGELOG" >> $GITHUB_OUTPUT` pattern

