<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.58

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.58** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference actions by mutable tag or branch instead of a pinned 40-character SHA commit hash, making them vulnerable to supply-chain attacks. Failing refs: ci.yml — actions/checkout@v4 (×5); code-review.yml — actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×7); release.yml — actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:21`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. This grants the default, overly broad token permissions to every job.

Locations:

- `.github/workflows/ci.yml:1`

### hardcoded-credentials (severity: high)

docker-compose.yml contains hardcoded literal passwords: POSTGRES_PASSWORD: changeme (line 24) and DB_PASSWORD: changeme (line 46). These are not GitHub Actions secret expressions and represent real hardcoded credentials committed to the repository.

Locations:

- `docker-compose.yml:24`
- `docker-compose.yml:46`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes $CHANGELOG (populated from git log commit messages, which are attacker-controllable via crafted commit messages in PRs or pushes) directly to $GITHUB_OUTPUT without sanitization using printf '%s' ... | tr -d '\n\r'. A malicious commit message containing a newline followed by 'key=injected_value' could inject arbitrary key-value pairs into GITHUB_OUTPUT, potentially influencing downstream steps.

Locations:

- `.github/workflows/release.yml:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, github-env-injection

**Notes:**

Fixed all four findings: (1) unpinned-uses: pinned all action references to full 40-char SHAs in ci.yml (actions/checkout@v4 ×5), code-review.yml (actions/checkout@v6 ×5, sulthonzh/code-reviewer@main ×7), and release.yml (actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 ×2, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1); (2) missing-permissions: added top-level 'permissions: contents: read' to ci.yml; (3) hardcoded-credentials: replaced POSTGRES_PASSWORD: changeme and DB_PASSWORD: changeme in docker-compose.yml with ${POSTGRES_PASSWORD} and ${DB_PASSWORD} environment variable references; (4) github-env-injection: sanitized the changelog step in release.yml by stripping carriage returns from CHANGELOG with tr -d '\r' and stripping newlines from TAG with tr -d '\n\r' before writing to GITHUB_OUTPUT.

