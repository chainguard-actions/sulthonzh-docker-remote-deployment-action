<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.69

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.69** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of full 40-character SHA commit pins, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/ci.yml: uses: actions/checkout@v4 (×5 occurrences)

.github/workflows/code-review.yml: uses: actions/checkout@v6 (×5 occurrences), uses: sulthonzh/code-reviewer@main (×7 occurrences — branch ref)

.github/workflows/release.yml: uses: actions/checkout@v4, uses: docker/setup-qemu-action@v3, uses: docker/setup-buildx-action@v3, uses: docker/login-action@v3 (×2), uses: docker/metadata-action@v5, uses: docker/build-push-action@v5, uses: softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:42`
- `.github/workflows/code-review.yml:17`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:33`
- `.github/workflows/code-review.yml:37`
- `.github/workflows/code-review.yml:48`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:64`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:86`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:49`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:76`

### permissions (severity: medium)

missing-permissions: ci.yml has no top-level 'permissions:' key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level 'permissions:' block. This means the workflow runs with the default, overly-broad token permissions.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable $CHANGELOG is populated from git commit messages (which can be controlled by an attacker who can push commits) and then written directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' "$CHANGELOG" | tr -d '\n\r'). A malicious commit message containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially poisoning downstream step outputs.

Offending lines:
  echo "$CHANGELOG" >> $GITHUB_OUTPUT

Locations:

- `.github/workflows/release.yml:72`

### hardcoded-credentials (severity: high)

docker-compose.yml contains two hardcoded literal passwords: 'POSTGRES_PASSWORD: changeme' (line 24) and 'DB_PASSWORD: changeme' (line 44). These are non-expression literal credential values that should be replaced with secret references or environment variable injection.

Locations:

- `docker-compose.yml:24`
- `docker-compose.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, github-env-injection, hardcoded-credentials

**Notes:**

1. unpinned-uses: Pinned all action refs to full SHAs with tag comments — actions/checkout@v4→11d5960a, actions/checkout@v6→d23441a4, sulthonzh/code-reviewer@main→d882af6c, docker/setup-qemu-action@v3→c7c53464, docker/setup-buildx-action@v3→8d2750c6, docker/login-action@v3→c94ce9fb, docker/metadata-action@v5→c299e40c, docker/build-push-action@v5→ca052bb5, softprops/action-gh-release@v1→de2c0eb8.
2. permissions: Added top-level `permissions: contents: read` to ci.yml.
3. github-env-injection: In release.yml's changelog step, TAG is sanitized via `tr -d '\n\r'` before writing to GITHUB_OUTPUT, and CHANGELOG is sanitized via `tr -d '\r'` before the heredoc write.
4. hardcoded-credentials: Replaced literal `changeme` passwords in docker-compose.yml with `${POSTGRES_PASSWORD}` and `${DB_PASSWORD}` environment variable references.

