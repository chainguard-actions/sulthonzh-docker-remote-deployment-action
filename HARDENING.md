<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action--/v1.4.40

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action--/v1.4.40** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags/branches instead of pinned full-length SHA commit hashes, making them vulnerable to supply-chain attacks.

ci.yml: actions/checkout@v4 (×5 steps)
code-review.yml: actions/checkout@v6 (×multiple steps), sulthonzh/code-reviewer@main (×7 steps)
release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/release.yml:14`

### permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define job-level permissions. This means the workflow runs with the default, overly broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/ci.yml:1`

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded literal password: 'DB_PASSWORD: mypassword'. Even in example/test compose files shipped with the action, hardcoded credentials are a security risk as they may be used as-is in production deployments.

Locations:

- `docker-compose.yml:43`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes values derived from potentially attacker-controlled sources to $GITHUB_OUTPUT without sanitization (no 'printf | tr -d newlines' step):
1. 'echo "tag=$TAG" >> $GITHUB_OUTPUT' — TAG is derived from $GITHUB_REF (a git tag name that could contain newlines or special characters).
2. 'echo "$CHANGELOG" >> $GITHUB_OUTPUT' — CHANGELOG is derived from git commit messages (attacker-controlled via PR/commit titles) and written directly without sanitization, enabling newline injection into GITHUB_OUTPUT.

Locations:

- `.github/workflows/release.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, hardcoded-credentials, github-env-injection

**Notes:**

1. unpinned-uses: Pinned all action references to full commit SHAs in ci.yml (actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 ×5), code-review.yml (actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 ×6, sulthonzh/code-reviewer@main → @d0c6f9c936438fbd487b575f55f739ba52f4cc37 ×7), and release.yml (actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 ×2, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1 all pinned to full SHAs). 2. permissions: Added top-level 'permissions: contents: read' to ci.yml. 3. hardcoded-credentials: Replaced 'DB_PASSWORD: mypassword' in docker-compose.yml with '${DB_PASSWORD:?DB_PASSWORD environment variable must be set}' to require the value be supplied via environment variable. 4. github-env-injection: Fixed the Generate changelog step in release.yml to sanitize TAG (via printf | tr -d newlines) before writing to GITHUB_OUTPUT, and sanitize CHANGELOG (stripping carriage returns) before writing via heredoc with a unique delimiter (__CHANGELOG_EOF__) to prevent injection.

