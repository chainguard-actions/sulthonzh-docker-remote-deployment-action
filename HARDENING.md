<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.32

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.32** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of pinned 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks where a compromised or updated action tag could execute malicious code.

**.github/workflows/ci.yml** — all `uses:` references are unpinned:
- `actions/checkout@v4` (×5)

**.github/workflows/code-review.yml** — all `uses:` references are unpinned:
- `actions/checkout@v6` (×4)
- `sulthonzh/code-reviewer@main` (×8) — branch ref is especially dangerous

**.github/workflows/release.yml** — all `uses:` references are unpinned:
- `actions/checkout@v4` (×2)
- `docker/setup-qemu-action@v3`
- `docker/setup-buildx-action@v3`
- `docker/login-action@v3` (×2)
- `docker/metadata-action@v5`
- `docker/build-push-action@v5`
- `softprops/action-gh-release@v1`

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:16`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:29`
- `.github/workflows/code-review.yml:33`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:62`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:84`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:29`
- `.github/workflows/release.yml:35`
- `.github/workflows/release.yml:42`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:64`
- `.github/workflows/release.yml:86`
- `.github/workflows/release.yml:100`

### missing-permissions (severity: medium)

`.github/workflows/ci.yml` has no top-level `permissions:` block and none of its five jobs (`shell-lint`, `dockerfile-lint`, `validate-yaml`, `security-scan`, `build-image`) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., `write` access to contents and packages). A minimal permissions block such as `permissions: read-all` or specific scopes should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across ci.yml, code-review.yml, and release.yml by replacing mutable tags/branch refs with full 40-character SHA commit hashes (preserving the original tag in a comment). Added a top-level `permissions: contents: read` block to ci.yml which was missing any permissions declaration. All SHAs were resolved using lookup_action_sha: actions/checkout@v4→11d5960a, actions/checkout@v6→d23441a4, sulthonzh/code-reviewer@main→d882af6c, docker/setup-qemu-action@v3→c7c53464, docker/setup-buildx-action@v3→8d2750c6, docker/login-action@v3→c94ce9fb, docker/metadata-action@v5→c299e40c, docker/build-push-action@v5→ca052bb5, softprops/action-gh-release@v1→de2c0eb8.

### Iteration 2

**Fixes applied:** suspicious-run-content, github-env-injection

**Notes:**

Fixed two security findings:

1. suspicious-run-content (docker-entrypoint.sh):
   - Line ~222: Replaced `eval $(ssh-agent)` with safe output parsing using grep -oP to extract SSH_AUTH_SOCK and SSH_AGENT_PID without eval, then exporting them.
   - Line ~340: Replaced `eval "${DEPLOYMENT_COMMAND} ${INPUT_ARGS}"` with a bash array approach: `read -ra CMD_ARRAY <<< "${DEPLOYMENT_COMMAND}"` followed by `"${CMD_ARRAY[@]}" ${INPUT_ARGS}`, eliminating eval with user-controlled input.

2. github-env-injection (.github/workflows/release.yml):
   - TAG: Added `safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')` before writing to GITHUB_OUTPUT.
   - CHANGELOG: Used a random hex delimiter via `openssl rand -hex 16` to prevent EOF injection from crafted commit messages, and stripped carriage returns with `tr -d '\r'` before writing to GITHUB_OUTPUT.

