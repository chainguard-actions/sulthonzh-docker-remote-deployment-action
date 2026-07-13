<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.55

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.55** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag/branch refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

- ci.yml: `actions/checkout@v4` used in 5 steps
- code-review.yml: `actions/checkout@v6` used in 5 steps; `sulthonzh/code-reviewer@main` used in 7 steps (branch ref — highest risk)
- release.yml: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, `softprops/action-gh-release@v1`

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:33`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:65`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:34`
- `.github/workflows/release.yml:41`
- `.github/workflows/release.yml:53`
- `.github/workflows/release.yml:70`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its 5 jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. This means the workflow runs with the default, overly-broad GITHUB_TOKEN permissions (read for most scopes, write for contents/packages on push events).

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes `$CHANGELOG` — populated from `git log` commit messages — to `$GITHUB_OUTPUT` without sanitization (`printf '%s' ... | tr -d '\n\r'`). Commit messages are attacker-controllable (any contributor can craft a commit message containing newlines or special sequences), enabling injection of arbitrary key=value pairs into the GitHub output context. The `$TAG` value derived from `$GITHUB_REF` is also written unsanitized. Affected lines: `echo "tag=$TAG" >> $GITHUB_OUTPUT` and `echo "$CHANGELOG" >> $GITHUB_OUTPUT`.

Locations:

- `.github/workflows/release.yml:77`
- `.github/workflows/release.yml:87`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. **unpinned-uses** (ci.yml, code-review.yml, release.yml):
   - `actions/checkout@v4` → pinned to SHA `34e114876b0b11c390a56381ad16ebd13914f8d5` (ci.yml ×5, release.yml ×2)
   - `actions/checkout@v6` → pinned to SHA `df4cb1c069e1874edd31b4311f1884172cec0e10` (code-review.yml ×5)
   - `sulthonzh/code-reviewer@main` → pinned to SHA `4546aa400043740f47f0023b54663fdb592b253f` (code-review.yml ×7)
   - `docker/setup-qemu-action@v3` → pinned to SHA `c7c53464625b32c7a7e944ae62b3e17d2b600130`
   - `docker/setup-buildx-action@v3` → pinned to SHA `8d2750c68a42422c14e847fe6c8ac0403b4cbd6f`
   - `docker/login-action@v3` → pinned to SHA `c94ce9fb468520275223c153574b00df6fe4bcc9` (×2)
   - `docker/metadata-action@v5` → pinned to SHA `c299e40c65443455700f0fdfc63efafe5b349051`
   - `docker/build-push-action@v5` → pinned to SHA `ca052bb54ab0790a636c9b5f226502c73d547a25`
   - `softprops/action-gh-release@v1` → pinned to SHA `de2c0eb89ae2a093876385947365aca7b0e5f844`

2. **missing-permissions** (ci.yml): Added `permissions: {}` at the top level to restrict the GITHUB_TOKEN to no permissions by default.

3. **github-env-injection** (release.yml): The 'Generate changelog' step now sanitizes the TAG value using `printf '%s' "$RAW_TAG" | tr -d '\n\r'` before writing to GITHUB_OUTPUT. The CHANGELOG is written using a randomized heredoc delimiter (generated with `openssl rand -hex 8`) to prevent delimiter injection, and carriage returns are stripped with `tr -d '\r'`.

