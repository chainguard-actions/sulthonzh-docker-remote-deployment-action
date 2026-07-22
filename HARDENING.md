<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.6** was hardened automatically. 3 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in .github/workflows/main.yml use mutable version tags instead of full 40-character SHA commit hashes, making the workflow vulnerable to supply-chain attacks if the referenced action tags are moved or compromised. Unpinned references: `actions/checkout@v6`, `docker/setup-qemu-action@v4`, `docker/setup-buildx-action@v4`, `docker/login-action@v4`, `docker/build-push-action@v7`.

Locations:

- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:16`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:24`
- `.github/workflows/main.yml:30`

### unpinned-uses (severity: high)

All `uses:` references in .github/workflows/code-review.yml use mutable version tags or branch names instead of full 40-character SHA commit hashes. Unpinned references: `actions/checkout@v6` (multiple times) and `sulthonzh/code-reviewer@main` (multiple times — `@main` is a mutable branch reference and is especially dangerous).

Locations:

- `.github/workflows/code-review.yml:24`
- `.github/workflows/code-review.yml:30`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:47`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:65`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:89`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/code-review.yml:104`
- `.github/workflows/code-review.yml:109`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and the single `build` job also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes). A minimal permissions block should be added.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:
1. main.yml: Pinned all 5 action references to full SHAs (actions/checkout@d23441a48e516b6c34aea4fa41551a30e30af803, docker/setup-qemu-action@96fe6ef7f33517b61c61be40b68a1882f3264fb8, docker/setup-buildx-action@bb05f3f5519dd87d3ba754cc423b652a5edd6d2c, docker/login-action@af1e73f918a031802d376d3c8bbc3fe56130a9b0, docker/build-push-action@53b7df96c91f9c12dcc8a07bcb9ccacbed38856a). Added top-level permissions block with contents: read and packages: write (minimal for a Docker build/push release workflow).
2. code-review.yml: Pinned all 12 action references — 6 uses of actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 and 6 uses of sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c. The file already had a permissions block so no change was needed there.

### Iteration 1

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative: capture `ssh-agent -s` output into a shell variable, then extract SSH_AUTH_SOCK and SSH_AGENT_PID individually using `grep -oP` with precise lookbehind patterns, export them explicitly, and unset the temporary variable. This eliminates the eval-with-command-substitution pattern while preserving full functional equivalence.

### Iteration 2

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced the hardcoded plaintext password `DB_PASSWORD: mypassword` in docker-compose.yml (line 44) with `DB_PASSWORD: ${DB_PASSWORD}`. Docker Compose will now read the value from the host environment at runtime instead of embedding it in the file. Operators should supply the credential via a secrets manager, CI/CD secret injection, or a `.env` file that is excluded from version control.

