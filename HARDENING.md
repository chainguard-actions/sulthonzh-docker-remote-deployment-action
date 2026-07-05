<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action--/v1.4.38

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action--/v1.4.38** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All uses: references in ci.yml use mutable tags (@v4) instead of pinned 40-character SHA digests, making the workflow vulnerable to supply-chain attacks if the referenced action is compromised or the tag is moved. Failing references: actions/checkout@v4 (appears 5 times).

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:44`

### unpinned-uses (severity: high)

All uses: references in code-review.yml use mutable tags or branch names instead of pinned 40-character SHA digests. Failing references: actions/checkout@v6 (multiple), sulthonzh/code-reviewer@main (multiple). Using @main is especially dangerous as it tracks a mutable branch.

Locations:

- `.github/workflows/code-review.yml:24`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:45`
- `.github/workflows/code-review.yml:52`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:77`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:99`
- `.github/workflows/code-review.yml:107`

### unpinned-uses (severity: high)

All uses: references in release.yml use mutable tags instead of pinned 40-character SHA digests. Failing references: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:37`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:65`
- `.github/workflows/release.yml:75`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and no job-level permissions: block on any of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image). Without explicit permissions, the workflow inherits the repository default, which may be overly permissive.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files: (1) ci.yml: pinned all 5 actions/checkout@v4 to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5 and added top-level 'permissions: contents: read' block; (2) code-review.yml: pinned all 6 actions/checkout@v6 occurrences to SHA df4cb1c069e1874edd31b4311f1884172cec0e10 and all 6 sulthonzh/code-reviewer@main occurrences to SHA d0c6f9c936438fbd487b575f55f739ba52f4cc37; (3) release.yml: pinned actions/checkout@v4 (x2) to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5, docker/setup-qemu-action@v3 to c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 to 8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 (x2) to c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 to c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 to ca052bb54ab0790a636c9b5f226502c73d547a25, and softprops/action-gh-release@v1 to de2c0eb89ae2a093876385947365aca7b0e5f844. All SHAs were resolved via lookup_action_sha and preserved with tag comments for readability.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the 'Generate changelog' step in .github/workflows/release.yml:
1. Sanitized the TAG value using `printf '%s' "$TAG" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT.
2. Replaced the static 'EOF' heredoc delimiter with a cryptographically random delimiter (`CHANGELOG_DELIM_$(openssl rand -hex 16)`) to prevent commit messages containing 'EOF' from breaking out of the heredoc and injecting arbitrary keys.
3. Added defense-in-depth by filtering out any lines in $CHANGELOG that match the random delimiter using `grep -v`.
4. Used `printf` for precise control over the multiline output format written to $GITHUB_OUTPUT.
5. Properly quoted $GITHUB_OUTPUT throughout.

