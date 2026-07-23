<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.63

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.63** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions by mutable tag or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks.

ci.yml: uses actions/checkout@v4 in all 5 jobs.

code-review.yml: uses actions/checkout@v6 (4 steps) and sulthonzh/code-reviewer@main (7 steps). The @main branch reference is especially dangerous as any push to that branch immediately affects all callers.

release.yml: uses actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1. All should be pinned to full SHA digests.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:40`
- `.github/workflows/code-review.yml:17`
- `.github/workflows/code-review.yml:21`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/code-review.yml:36`
- `.github/workflows/code-review.yml:50`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/release.yml:16`
- `.github/workflows/release.yml:19`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:30`
- `.github/workflows/release.yml:35`
- `.github/workflows/release.yml:46`
- `.github/workflows/release.yml:61`
- `.github/workflows/release.yml:77`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions the workflow inherits the repository default token permissions, which may grant write access to contents, packages, and other scopes. A minimal `permissions: contents: read` block should be added at the top level or to each job.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable $CHANGELOG — populated from `git log` output — is written directly to $GITHUB_OUTPUT without newline sanitization:

  CHANGELOG=$(git log $LAST_TAG..HEAD --pretty=format:"- %s (%h)")
  echo "changelog<<EOF" >> $GITHUB_OUTPUT
  echo "$CHANGELOG" >> $GITHUB_OUTPUT   # FAIL: no tr -d newlines
  echo "EOF" >> $GITHUB_OUTPUT

Commit messages are attacker-controllable: a crafted commit subject containing a newline followed by `key=injected-value` would inject an extra key into GITHUB_OUTPUT. The $TAG value (from ${GITHUB_REF#refs/tags/}) is also written unsanitized: `echo "tag=$TAG" >> $GITHUB_OUTPUT`. Both writes must be preceded by `safe=$(printf '%s' "$VAR" | tr -d '\n\r')` before the echo.

Locations:

- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:59`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. unpinned-uses: Pinned all 9 distinct action references to full 40-char commit SHAs with tag comments preserved. This covers all 25 locations across ci.yml (5×actions/checkout@v4), code-review.yml (5×actions/checkout@v6, 7×sulthonzh/code-reviewer@main), and release.yml (2×actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, 2×docker/login-action@v3, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1).

2. missing-permissions: Added `permissions: contents: read` at the top level of ci.yml, which is the minimum needed for a CI workflow that only reads the repository.

3. github-env-injection: In release.yml's 'Generate changelog' step, the TAG value is now sanitized with `printf '%s' "$RAW_TAG" | tr -d '\n\r'` before writing to GITHUB_OUTPUT. The CHANGELOG multi-line value now uses a randomly-generated hex delimiter (via `openssl rand -hex 16`) instead of the static 'EOF' string, preventing an attacker from injecting a commit message containing 'EOF' to terminate the heredoc early and inject additional key=value pairs into GITHUB_OUTPUT.

