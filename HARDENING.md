<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.31

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.31** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

docker-compose.yml contains a hardcoded literal password: `DB_PASSWORD: mypassword`. This is an example compose file shipped with the action, but it contains a real plaintext credential that should be replaced with a secret reference or placeholder comment.

Locations:

- `docker-compose.yml:43`

### unpinned-uses (severity: high)

All four workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised.

ci.yml: actions/checkout@v4 (×5)
code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×7)
main.yml: actions/checkout@v6, docker/setup-qemu-action@v4, docker/setup-buildx-action@v4, docker/login-action@v4, docker/build-push-action@v7
release.yml: actions/checkout@v4 (×2), docker/setup-buildx-action@v3, docker/login-action@v3, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:16`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:24`
- `.github/workflows/main.yml:30`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:56`
- `.github/workflows/release.yml:79`

### missing-permissions (severity: medium)

ci.yml and main.yml have no top-level `permissions:` key and no job-level `permissions:` on any of their jobs. Without explicit permissions, workflows run with the default (potentially write) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/main.yml:1`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes attacker-controllable data to $GITHUB_OUTPUT without sanitization. Specifically: (1) `$CHANGELOG` is populated from `git log` commit messages — an attacker can craft a commit message containing newlines to inject arbitrary key=value pairs into GITHUB_OUTPUT; (2) `$TAG` is derived from `$GITHUB_REF` and also written unsanitized. Neither value is passed through `printf '%s' ... | tr -d '\n\r'` before the write. The multiline heredoc `changelog<<EOF` pattern is also vulnerable to EOF injection via a commit message containing a bare `EOF` line.

Locations:

- `.github/workflows/release.yml:48`
- `.github/workflows/release.yml:55`
- `.github/workflows/release.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. docker-compose.yml: Replaced hardcoded `DB_PASSWORD: mypassword` with `DB_PASSWORD: "${DB_PASSWORD}"` and a comment directing users to set it as an environment variable or secret.

2. All workflow files: Pinned every mutable tag/branch reference to its full 40-character SHA digest:
   - ci.yml: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (×5)
   - code-review.yml: actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 (×5); sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c (×7)
   - main.yml: checkout@v6, setup-qemu-action@v4, setup-buildx-action@v4, login-action@v4, build-push-action@v7 — all pinned
   - release.yml: checkout@v4 (×2), setup-buildx-action@v3, login-action@v3, metadata-action@v5, build-push-action@v5, action-gh-release@v1 — all pinned

3. ci.yml and main.yml: Added `permissions: contents: read` top-level block to enforce least privilege.

4. release.yml 'Generate changelog' step: Moved `github.ref` into the step's `env:` block; sanitized TAG with `printf '%s' | tr -d '\n\r'`; replaced the static `EOF` heredoc delimiter with a cryptographically random delimiter (`openssl rand -hex 16`) to prevent EOF injection via crafted commit messages.

