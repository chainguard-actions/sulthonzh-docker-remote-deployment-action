<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.50

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.50** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files use mutable tag or branch refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (×5 steps)

code-review.yml: actions/checkout@v6 (×5 steps), sulthonzh/code-reviewer@main (×7 steps)

release.yml: actions/checkout@v4 (×2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:50`
- `.github/workflows/code-review.yml:64`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:87`
- `.github/workflows/code-review.yml:94`
- `.github/workflows/code-review.yml:104`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/code-review.yml:116`
- `.github/workflows/code-review.yml:122`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:66`
- `.github/workflows/release.yml:87`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. Without explicit permissions, the workflow inherits the repository default (typically write access to all scopes), violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable $CHANGELOG is populated from git commit messages (git log --pretty=format:'- %s (%h)') and then written directly to $GITHUB_OUTPUT without sanitization (no printf '%s' ... | tr -d '\n\r' step). Git commit messages are user-controlled content: any contributor can craft a commit message containing newlines or special characters that could inject additional key=value pairs into GITHUB_OUTPUT, potentially poisoning downstream steps that consume those outputs. The offending line is: echo "$CHANGELOG" >> $GITHUB_OUTPUT

Locations:

- `.github/workflows/release.yml:83`

### script-injection (severity: high)

Rule (b) violation in the 'Generate changelog' step of release.yml: the shell variable $LAST_TAG (derived from 'git describe --tags') is used unquoted in a git command: git log $LAST_TAG..HEAD --pretty=format:"- %s (%h)". Tag names are user-controlled (anyone with push access can create a tag with shell metacharacters). The unquoted expansion allows the shell to parse metacharacters out of the tag name, enabling command injection. The variable must be double-quoted: git log "$LAST_TAG"..HEAD ...

Locations:

- `.github/workflows/release.yml:78`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection, github-env-injection

**Notes:**

Fixed all four findings across three workflow files:

1. **unpinned-uses**: Pinned all action references to full 40-char SHAs with tag comments:
   - ci.yml: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (×5)
   - code-review.yml: actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 (×5), sulthonzh/code-reviewer@main → @4546aa400043740f47f0023b54663fdb592b253f (×7)
   - release.yml: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (×2), docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 (×2), docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844

2. **missing-permissions**: Added `permissions: contents: read` top-level block to ci.yml.

3. **script-injection**: Fixed unquoted `$LAST_TAG` in release.yml's git log command to use `"$LAST_TAG"` (double-quoted) to prevent shell metacharacter injection from user-controlled tag names.

4. **github-env-injection**: Sanitized TAG and CHANGELOG values before writing to $GITHUB_OUTPUT using `printf '%s' | tr -d '\n\r'` for the tag and `tr -d '\r'` for the multiline changelog (which uses heredoc syntax so newlines are intentional but carriage returns are stripped to prevent injection).

