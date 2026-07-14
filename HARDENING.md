<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.57

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.57** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (×5)
code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×8)
release.yml: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:47`
- `.github/workflows/code-review.yml:21`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:41`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:31`
- `.github/workflows/release.yml:36`
- `.github/workflows/release.yml:46`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:72`
- `.github/workflows/release.yml:90`

### hardcoded-credentials (severity: high)

docker-compose.yml contains two hardcoded literal passwords ('changeme') assigned to POSTGRES_PASSWORD and DB_PASSWORD. Even though the file is labeled as an example, it ships with the action and could be used as-is, exposing the database to unauthorized access.

Locations:

- `docker-compose.yml:24`
- `docker-compose.yml:44`

### missing-permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level 'permissions:' block. This means the workflow runs with the default broad permissions granted by GitHub, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, hardcoded-credentials, missing-permissions

**Notes:**

1. unpinned-uses: Pinned all action references to full 40-char SHAs with tag comments: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c, docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844. 2. hardcoded-credentials: Replaced both 'changeme' literal passwords in docker-compose.yml with ${POSTGRES_PASSWORD} and ${DB_PASSWORD} variable interpolation, requiring values to be supplied via environment or .env file. 3. missing-permissions: Added top-level 'permissions: contents: read' block to ci.yml to enforce least privilege.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection vulnerabilities in .github/workflows/release.yml:
1. Line 77 (TAG): Added `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` sanitization before writing to GITHUB_OUTPUT, preventing newline injection from GITHUB_REF.
2. Line 87 (CHANGELOG): Sanitized git log output by stripping carriage returns with `tr -d '\r'`, replacing any line equal to the heredoc delimiter with `sed 's/^EOF$/EOF_/g'`, and using a unique delimiter `CHANGELOG_DELIM` instead of `EOF` to prevent heredoc delimiter injection from attacker-controlled commit messages.

