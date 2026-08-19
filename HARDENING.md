<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.13** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or the tag is moved.

.github/workflows/code-review.yml:
  - uses: actions/checkout@v6
  - uses: sulthonzh/code-reviewer@main (used 6 times across multiple steps)

.github/workflows/main.yml:
  - uses: actions/checkout@v6
  - uses: docker/setup-qemu-action@v4
  - uses: docker/setup-buildx-action@v4
  - uses: docker/login-action@v4
  - uses: docker/build-push-action@v7

All of these should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:53`
- `.github/workflows/code-review.yml:63`
- `.github/workflows/code-review.yml:69`
- `.github/workflows/code-review.yml:78`
- `.github/workflows/code-review.yml:86`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/main.yml:13`
- `.github/workflows/main.yml:16`
- `.github/workflows/main.yml:19`
- `.github/workflows/main.yml:22`
- `.github/workflows/main.yml:28`

### permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. Explicit minimal permissions should be declared.

Locations:

- `.github/workflows/main.yml:1`

### suspicious-run-content (severity: high)

Sub-check: eval-dynamic. The file docker-entrypoint.sh contains `eval $(ssh-agent)`, which matches the eval-dynamic pattern (`eval` followed by command substitution `$(...)`). While this is a common pattern for initialising the SSH agent, it executes the output of a command substitution via eval, which is the same construct used to obfuscate and execute dynamic shell payloads. The pattern is flagged per the eval-dynamic check rule.

Locations:

- `docker-entrypoint.sh:163`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action references to full SHA hashes in both workflow files: actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803, sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c, docker/setup-qemu-action@v4 → @96fe6ef7f33517b61c61be40b68a1882f3264fb8, docker/setup-buildx-action@v4 → @bb05f3f5519dd87d3ba754cc423b652a5edd6d2c, docker/login-action@v4 → @af1e73f918a031802d376d3c8bbc3fe56130a9b0, docker/build-push-action@v7 → @53b7df96c91f9c12dcc8a07bcb9ccacbed38856a. Original tags preserved as inline comments. 2. permissions: Added top-level `permissions: contents: read` to main.yml (code-review.yml already had explicit permissions). 3. suspicious-run-content: Replaced `eval $(ssh-agent)` in docker-entrypoint.sh with a safe alternative using `ssh-agent -a <socket>` to bind to a known socket path, then extracting SSH_AGENT_PID via grep from the agent output, and exporting both SSH_AUTH_SOCK and SSH_AGENT_PID directly without eval.

