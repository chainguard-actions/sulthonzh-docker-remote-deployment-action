<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.64

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.64** was hardened automatically. 4 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All uses: references in ci.yml are pinned to mutable version tags (@v4) instead of immutable 40-character commit SHAs. Failing references: actions/checkout@v4 (×5 occurrences across all jobs). A supply-chain attacker who compromises the upstream action repository can push malicious code to the same tag.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:46`

### unpinned-uses (severity: high)

All uses: references in code-review.yml are pinned to mutable version tags or branch names instead of immutable 40-character commit SHAs. Failing references: actions/checkout@v6 (×5 occurrences) and sulthonzh/code-reviewer@main (×7 occurrences). The @main branch reference is especially dangerous as it tracks the tip of the default branch and can be updated at any time by the repository owner.

Locations:

- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:90`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/code-review.yml:104`

### unpinned-uses (severity: high)

All uses: references in release.yml are pinned to mutable version tags instead of immutable 40-character commit SHAs. Failing references: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:49`
- `.github/workflows/release.yml:62`
- `.github/workflows/release.yml:79`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.).

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by resolving each tag/branch to its full 40-character commit SHA (preserving the original tag in a comment). Added a top-level `permissions: contents: read` block to ci.yml to address the missing-permissions finding. Specific changes: ci.yml — 5× actions/checkout@v4 pinned to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5, plus top-level permissions block added; code-review.yml — 5× actions/checkout@v6 pinned to SHA df4cb1c069e1874edd31b4311f1884172cec0e10, 7× sulthonzh/code-reviewer@main pinned to SHA d882af6cd1ae55f692c0a8dfc6ff464115ccf89c; release.yml — 2× actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, 2× docker/login-action@v3, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1 all pinned to their respective SHAs.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed the 'Generate changelog' step in .github/workflows/release.yml to sanitize attacker-controlled git commit messages before writing to $GITHUB_OUTPUT. Changes: (1) Sanitized TAG with `printf '%s' "$TAG" | tr -d '\n\r'` before writing to GITHUB_OUTPUT. (2) Sanitized CHANGELOG with `grep -v '^EOF$'` to prevent heredoc delimiter injection and `tr -d '\r'` to strip carriage returns. (3) Used `printf '%s\n'` instead of `echo` for writing sanitized content. (4) Quoted $GITHUB_OUTPUT and the git log range argument throughout.

### Iteration 3

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced both hardcoded plaintext passwords in docker-compose.yml with environment variable interpolation references: `POSTGRES_PASSWORD: changeme` → `POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}` (line 24) and `DB_PASSWORD: changeme` → `DB_PASSWORD: ${DB_PASSWORD}` (line 45). Operators must now supply these values via the host environment or a `.env` file, keeping secrets out of source control.

