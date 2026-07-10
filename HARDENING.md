<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.45

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.45** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All uses: references in ci.yml are pinned to mutable version tags (@v4) rather than full 40-character SHA digests. Failing references: actions/checkout@v4 (×5 occurrences).

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:44`

### unpinned-uses (severity: high)

All uses: references in code-review.yml are pinned to mutable tags or branch names rather than full 40-character SHA digests. Failing references: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×7). The @main branch reference is especially dangerous as it tracks a moving branch head.

Locations:

- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:69`
- `.github/workflows/code-review.yml:76`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:113`
- `.github/workflows/code-review.yml:120`

### unpinned-uses (severity: high)

All uses: references in release.yml are pinned to mutable version tags rather than full 40-character SHA digests. Failing references: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:29`
- `.github/workflows/release.yml:34`
- `.github/workflows/release.yml:42`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:72`
- `.github/workflows/release.yml:89`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by resolving each tag to its full 40-character SHA digest using lookup_action_sha. Added a top-level `permissions: {}` block to ci.yml to address the missing-permissions finding. Specific changes: (1) ci.yml: pinned 5× actions/checkout@v4 → SHA 34e114876b0b11c390a56381ad16ebd13914f8d5, added `permissions: {}`; (2) code-review.yml: pinned 5× actions/checkout@v6 → SHA df4cb1c069e1874edd31b4311f1884172cec0e10, pinned 7× sulthonzh/code-reviewer@main → SHA d0c6f9c936438fbd487b575f55f739ba52f4cc37; (3) release.yml: pinned actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1 to their respective full SHAs.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed the 'Generate changelog' step in .github/workflows/release.yml: (1) Sanitized the TAG value using `printf '%s' "$TAG" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT to prevent newline injection. (2) Sanitized the CHANGELOG content by stripping carriage returns with `tr -d '\r'` and escaping any line that exactly matches the heredoc delimiter `CHANGELOG_DELIM` (replacing it with `CHANGELOG_DELIM_`) to prevent heredoc delimiter injection. (3) Used a unique named delimiter `CHANGELOG_DELIM` instead of the generic `EOF`. (4) Used `printf` for writing the multiline output to $GITHUB_OUTPUT. (5) Properly quoted `"$GITHUB_OUTPUT"` throughout.

