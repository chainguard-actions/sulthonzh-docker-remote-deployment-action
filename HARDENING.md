<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.9** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned full-length SHA digests, making them vulnerable to supply-chain attacks.

.github/workflows/code-review.yml:
- uses: actions/checkout@v6
- uses: sulthonzh/code-reviewer@main (used 7 times across all jobs)

.github/workflows/main.yml:
- uses: actions/checkout@v6
- uses: docker/setup-qemu-action@v4
- uses: docker/setup-buildx-action@v4
- uses: docker/login-action@v4
- uses: docker/build-push-action@v7

All of these should be pinned to a full 40-character commit SHA (e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4).

Locations:

- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:74`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:16`
- `.github/workflows/main.yml:19`
- `.github/workflows/main.yml:22`
- `.github/workflows/main.yml:29`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). Add a minimal `permissions:` block at the top level or per job.

Locations:

- `.github/workflows/main.yml:1`

### hardcoded-credentials (severity: high)

The file docker-compose.yml contains a hardcoded literal password: `DB_PASSWORD: mypassword`. This credential is committed in plaintext and will be exposed to anyone with repository access. Replace with a secrets reference or environment variable injected at runtime.

Locations:

- `docker-compose.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials

**Notes:**

1. unpinned-uses: Pinned all action references to full 40-char SHAs in both workflow files:
   - code-review.yml: actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6; sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c # main (7 occurrences)
   - main.yml: actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6; docker/setup-qemu-action@v4 → @96fe6ef7f33517b61c61be40b68a1882f3264fb8 # v4; docker/setup-buildx-action@v4 → @bb05f3f5519dd87d3ba754cc423b652a5edd6d2c # v4; docker/login-action@v4 → @af1e73f918a031802d376d3c8bbc3fe56130a9b0 # v4; docker/build-push-action@v7 → @53b7df96c91f9c12dcc8a07bcb9ccacbed38856a # v7
2. missing-permissions: Added top-level permissions block to main.yml with 'contents: read' and 'packages: write' (minimal permissions needed for a Docker build-and-push release workflow).
3. hardcoded-credentials: Replaced 'DB_PASSWORD: mypassword' in docker-compose.yml with 'DB_PASSWORD: ${DB_PASSWORD:?DB_PASSWORD environment variable must be set}' — this uses Docker Compose variable substitution with a mandatory error if the variable is not set at runtime, preventing accidental use without a proper secret.

