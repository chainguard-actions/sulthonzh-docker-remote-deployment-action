<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action--/v1.4.37

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action--/v1.4.37** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks. ci.yml: actions/checkout@v4 (×5). code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×7 — branch ref). release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. Without explicit permissions, the workflow inherits the repository default (often write-all), granting unnecessary access.

Locations:

- `.github/workflows/ci.yml:1`

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded literal password: DB_PASSWORD: mypassword. This is an example/demo file committed to the repository with a plaintext credential. Even example credentials should not be hardcoded; they should reference environment variables or secrets.

Locations:

- `docker-compose.yml:44`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes git log output (commit messages, which are attacker-controllable) directly to $GITHUB_OUTPUT without sanitization. An attacker can craft a commit message containing newlines to inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially poisoning subsequent steps. The CHANGELOG variable is written with: echo "$CHANGELOG" >> $GITHUB_OUTPUT — no printf '%s' ... | tr -d '\n\r' sanitization is applied.

Locations:

- `.github/workflows/release.yml:72`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, github-env-injection

**Notes:**

1. unpinned-uses: Pinned all action references to full 40-char SHAs in ci.yml (actions/checkout@v4 ×5), code-review.yml (actions/checkout@v6 ×5, sulthonzh/code-reviewer@main ×7), and release.yml (actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 ×2, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1). 2. missing-permissions: Added top-level 'permissions: contents: read' to ci.yml. 3. hardcoded-credentials: Replaced literal 'mypassword' in docker-compose.yml with '${DB_PASSWORD}' environment variable reference. 4. github-env-injection: Fixed the Generate changelog step in release.yml to sanitize the TAG and CHANGELOG values using printf + tr before writing to GITHUB_OUTPUT, preventing newline injection attacks.

