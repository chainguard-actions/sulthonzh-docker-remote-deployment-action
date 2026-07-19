<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.57

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.57** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions using mutable version tags or branch names instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved.

• ci.yml: `actions/checkout@v4` (×5 steps)
• code-review.yml: `actions/checkout@v6` (×5 steps), `sulthonzh/code-reviewer@main` (×7 steps)
• release.yml: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, `softprops/action-gh-release@v1`

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:44`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:37`
- `.github/workflows/code-review.yml:42`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:34`
- `.github/workflows/release.yml:47`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:82`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write-all by default on many repositories).

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, two unsanitized values are written to $GITHUB_OUTPUT without applying `printf '%s' ... | tr -d '\n\r'` sanitization:

1. `TAG=${GITHUB_REF#refs/tags/}` followed by `echo "tag=$TAG" >> $GITHUB_OUTPUT` — `GITHUB_REF` is a GitHub-controlled environment variable that can contain newlines, enabling output injection.

2. `CHANGELOG=$(git log ... --pretty=format:"- %s (%h)")` followed by `echo "$CHANGELOG" >> $GITHUB_OUTPUT` — git commit messages are attacker-controlled (any contributor can craft a commit message containing newlines or `key=value` pairs) and are written unsanitized to $GITHUB_OUTPUT, allowing injection of arbitrary output variables.

Locations:

- `.github/workflows/release.yml:64`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. **unpinned-uses** (ci.yml, code-review.yml, release.yml): Pinned all action references to full 40-char SHA commits with original tag as comment:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 # v6
   - sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c # main
   - docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130 # v3
   - docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3
   - docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 # v3
   - docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051 # v5
   - docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25 # v5
   - softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844 # v1

2. **missing-permissions** (ci.yml): Added top-level `permissions: {}` to restrict the GITHUB_TOKEN to no permissions by default, since the CI jobs only run local shell/docker commands.

3. **github-env-injection** (release.yml): Sanitized the 'Generate changelog' step — TAG is now stripped of newlines via `printf '%s' ... | tr -d '\n\r'` before writing to GITHUB_OUTPUT; CHANGELOG uses the safe heredoc `<<EOF` delimiter pattern and strips carriage returns to prevent injection via attacker-controlled commit messages.

