<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.43

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.43** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files use mutable tag or branch refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (used 5 times)
code-review.yml: actions/checkout@v6 (used 5 times), sulthonzh/code-reviewer@main (used 7 times — a branch ref)
release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and no job-level permissions: key on any of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image). Without explicit permissions, the workflow inherits the repository default, which may be overly broad (write access to all scopes).

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable $CHANGELOG (populated from git log commit messages, which are attacker-controllable via crafted commit messages) is written directly to $GITHUB_OUTPUT without sanitization (no `printf '%s' ... | tr -d '\n\r'` step). A commit message containing a newline followed by 'key=value' would inject an arbitrary key-value pair into GITHUB_OUTPUT. Similarly, $TAG (derived from GITHUB_REF) is written without sanitization.

Offending lines:
  echo "tag=$TAG" >> $GITHUB_OUTPUT
  echo "$CHANGELOG" >> $GITHUB_OUTPUT

Locations:

- `.github/workflows/release.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. unpinned-uses: Pinned all action refs to full SHAs with tag comments: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main → @d0c6f9c936438fbd487b575f55f739ba52f4cc37, docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844. 2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml. 3. github-env-injection: In release.yml's Generate changelog step, TAG is now sanitized with `printf '%s' | tr -d '\n\r'` before writing to GITHUB_OUTPUT, and the CHANGELOG multiline value uses a random hex delimiter (via `openssl rand -hex 16`) instead of the static 'EOF' to prevent heredoc injection via crafted commit messages.

