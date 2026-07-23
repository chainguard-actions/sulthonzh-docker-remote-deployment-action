<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.62

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.62** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/ci.yml: actions/checkout@v4 (lines 13, 20, 27, 35, 43)

.github/workflows/code-review.yml: actions/checkout@v6 (lines 20, 34, 57, 73, 88), sulthonzh/code-reviewer@main (lines 23, 37, 60, 76, 91, 96)

.github/workflows/release.yml: actions/checkout@v4 (lines 18, 52), docker/setup-qemu-action@v3 (line 21), docker/setup-buildx-action@v3 (line 24), docker/login-action@v3 (lines 27, 33), docker/metadata-action@v5 (line 38), docker/build-push-action@v5 (line 48), softprops/action-gh-release@v1 (line 70)

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:23`
- `.github/workflows/code-review.yml:34`
- `.github/workflows/code-review.yml:37`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:76`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:48`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:70`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow runs with the default token permissions, which may be overly broad (e.g., write access to contents on some repository configurations).

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, two values derived from external/untrusted sources are written to $GITHUB_OUTPUT without the required newline-stripping sanitization (`printf '%s' ... | tr -d '\n\r'`):

1. `echo "tag=$TAG" >> $GITHUB_OUTPUT` — $TAG is derived from `${GITHUB_REF#refs/tags/}`. A tag name containing a newline could inject additional key=value pairs into GITHUB_OUTPUT.

2. `echo "$CHANGELOG" >> $GITHUB_OUTPUT` — $CHANGELOG is populated from `git log` commit messages. Commit messages are attacker-controlled (any contributor can craft a commit message containing newlines) and are written verbatim to GITHUB_OUTPUT, allowing injection of arbitrary output variables.

Locations:

- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. unpinned-uses: Pinned all 9 distinct action references to their full 40-char SHA digests with original tag preserved as inline comments. Affected: ci.yml (5 occurrences of actions/checkout@v4), code-review.yml (6× actions/checkout@v6, 6× sulthonzh/code-reviewer@main), release.yml (2× actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, 2× docker/login-action@v3, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1).

2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml (the minimum needed for checkout-only CI jobs).

3. github-env-injection: In release.yml's 'Generate changelog' step, TAG is now sanitized with `printf '%s' "$RAW_TAG" | tr -d '\n\r'` before writing to GITHUB_OUTPUT, and CHANGELOG is sanitized with `printf '%s' "$CHANGELOG" | tr -d '\r'` before writing via the heredoc delimiter pattern.

