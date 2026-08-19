<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.11

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.11** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tags or branch names instead of pinned 40-character SHA commit digests, making the action vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/code-review.yml:
- `uses: actions/checkout@v6` (lines 28, 44, 75, 89, 100)
- `uses: sulthonzh/code-reviewer@main` (lines 31, 47, 60, 79, 83, 93, 104)

.github/workflows/main.yml:
- `uses: actions/checkout@v6` (line 12)
- `uses: docker/setup-qemu-action@v4` (line 15)
- `uses: docker/setup-buildx-action@v4` (line 18)
- `uses: docker/login-action@v4` (line 21)
- `uses: docker/build-push-action@v7` (line 27)

Locations:

- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:47`
- `.github/workflows/code-review.yml:60`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:83`
- `.github/workflows/code-review.yml:89`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/code-review.yml:104`
- `.github/workflows/main.yml:12`
- `.github/workflows/main.yml:15`
- `.github/workflows/main.yml:18`
- `.github/workflows/main.yml:21`
- `.github/workflows/main.yml:27`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/main.yml` has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g., `contents: write` by default on some repositories). Explicit minimal permissions should be declared.

Locations:

- `.github/workflows/main.yml:1`

### hardcoded-credentials (severity: high)

The file `docker-compose.yml` contains a hardcoded literal password: `DB_PASSWORD: mypassword`. Even in example/demo files committed to the repository, hardcoded credentials are a security risk as they may be copied into production use or indicate a pattern of insecure credential handling. Credentials should be supplied via environment variables or secrets management, never as literal values in committed files.

Locations:

- `docker-compose.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials

**Notes:**

1. unpinned-uses: Pinned all `uses:` references in .github/workflows/code-review.yml (actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803, sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c) and .github/workflows/main.yml (actions/checkout@v6, docker/setup-qemu-action@v4 → @96fe6ef7f33517b61c61be40b68a1882f3264fb8, docker/setup-buildx-action@v4 → @bb05f3f5519dd87d3ba754cc423b652a5edd6d2c, docker/login-action@v4 → @af1e73f918a031802d376d3c8bbc3fe56130a9b0, docker/build-push-action@v7 → @53b7df96c91f9c12dcc8a07bcb9ccacbed38856a). Original tags preserved as inline comments.
2. missing-permissions: Added top-level `permissions: contents: read` to .github/workflows/main.yml (minimal permission needed for checkout; Docker Hub credentials come from secrets).
3. hardcoded-credentials: Replaced `DB_PASSWORD: mypassword` with `DB_PASSWORD: ${DB_PASSWORD}` in docker-compose.yml so the password is supplied via environment variable at runtime rather than hardcoded in the file.

