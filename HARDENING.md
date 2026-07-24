<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.4** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

docker-compose.yml contains literal hardcoded passwords. The environment variables POSTGRES_PASSWORD and DB_PASSWORD are both set to the literal value 'mypassword' in plain text. These are not GitHub Actions secret expressions and represent hardcoded credentials.

Locations:

- `docker-compose.yml:27`
- `docker-compose.yml:46`

### unpinned-uses (severity: high)

All `uses:` references in main.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if those tags are moved. Failing references: actions/checkout@v6, docker/setup-qemu-action@v4, docker/setup-buildx-action@v4, docker/login-action@v4, docker/build-push-action@v7.

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:21`
- `.github/workflows/main.yml:25`
- `.github/workflows/main.yml:31`

### unpinned-uses (severity: high)

All `uses:` references in code-review.yml are pinned to mutable version tags or branch names rather than immutable 40-character commit SHAs. Failing references: actions/checkout@v6 (used multiple times) and sulthonzh/code-reviewer@main (used multiple times — @main is a branch reference and is especially dangerous).

Locations:

- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:113`
- `.github/workflows/code-review.yml:121`

### missing-permissions (severity: medium)

main.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes by default in many repository configurations).

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, unpinned-uses, missing-permissions

**Notes:**

1. docker-compose.yml: Replaced hardcoded 'mypassword' literals for POSTGRES_PASSWORD (line 27) and DB_PASSWORD (line 46) with Docker Compose variable interpolation syntax using mandatory variable enforcement (${VAR:?error message}), so the passwords must be supplied via environment variables at runtime and are never stored in the file.
2. .github/workflows/main.yml: Pinned all 5 `uses:` references to full 40-char commit SHAs (actions/checkout@d23441a4, docker/setup-qemu-action@96fe6ef7, docker/setup-buildx-action@bb05f3f5, docker/login-action@06fb636f, docker/build-push-action@53b7df96) and added a top-level `permissions: contents: read` block.
3. .github/workflows/code-review.yml: Pinned all 12 `uses:` references — actions/checkout@v6 → d23441a48e516b6c34aea4fa41551a30e30af803 and sulthonzh/code-reviewer@main → d882af6cd1ae55f692c0a8dfc6ff464115ccf89c — preserving the original tag/branch as inline comments.

