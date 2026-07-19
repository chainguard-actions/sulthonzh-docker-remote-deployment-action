<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.5** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files reference actions using mutable tags or branch names instead of immutable 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag or branch is overwritten.

.github/workflows/main.yml:
- actions/checkout@v6 (line 14)
- docker/setup-qemu-action@v4 (line 17)
- docker/setup-buildx-action@v4 (line 20)
- docker/login-action@v4 (line 23)
- docker/build-push-action@v7 (line 29)

.github/workflows/code-review.yml:
- actions/checkout@v6 (lines 27, 41, 65, 82, 91)
- sulthonzh/code-reviewer@main (lines 31, 45, 55, 69, 74, 84, 93)

All of these should be pinned to a full 40-character commit SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:23`
- `.github/workflows/main.yml:29`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/code-review.yml:41`
- `.github/workflows/code-review.yml:45`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:65`
- `.github/workflows/code-review.yml:69`
- `.github/workflows/code-review.yml:74`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:84`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:93`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes). A minimal permissions block should be added, e.g. `permissions: contents: read`.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed both workflow files:

1. `.github/workflows/main.yml`:
   - Added top-level `permissions: contents: read` block to address missing-permissions finding
   - Pinned all 5 action references to full SHA digests:
     - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10
     - docker/setup-qemu-action@v4 → @96fe6ef7f33517b61c61be40b68a1882f3264fb8
     - docker/setup-buildx-action@v4 → @bb05f3f5519dd87d3ba754cc423b652a5edd6d2c
     - docker/login-action@v4 → @af1e73f918a031802d376d3c8bbc3fe56130a9b0
     - docker/build-push-action@v7 → @53b7df96c91f9c12dcc8a07bcb9ccacbed38856a

2. `.github/workflows/code-review.yml`:
   - Pinned all action references to full SHA digests:
     - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 (5 occurrences)
     - sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c (7 occurrences)
   - The file already had a permissions block so no change was needed there

All original tags are preserved as inline comments (e.g., `# v4`, `# main`) for readability.

### Iteration 2

**Fixes applied:** hardcoded-credentials, suspicious-run-content

**Notes:**

1. docker-compose.yml: Replaced literal 'mypassword' at lines 28 and 48 with Docker Compose environment variable interpolation syntax '${POSTGRES_PASSWORD}'. The password must now be supplied via an environment variable (e.g., from a .env file or shell environment populated from GitHub Actions secrets). 2. docker-entrypoint.sh: Replaced 'eval $(ssh-agent)' with a safe alternative that runs 'ssh-agent -s > tmpfile', sources the tmpfile with '.', then removes it. This achieves the same result (setting SSH_AUTH_SOCK and SSH_AGENT_PID) without using the dangerous eval+command-substitution pattern.

