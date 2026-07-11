<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.51

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.51** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and no job-level `permissions:` block on any of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image). This means the workflow runs with the default (potentially broad) token permissions.

Locations:

- `.github/workflows/ci.yml:1`

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks:

ci.yml:
- actions/checkout@v4 (×5)

code-review.yml:
- actions/checkout@v6 (×5)
- sulthonzh/code-reviewer@main (×8, branch reference)

release.yml:
- actions/checkout@v4 (×2)
- docker/setup-qemu-action@v3
- docker/setup-buildx-action@v3
- docker/login-action@v3 (×2)
- docker/metadata-action@v5
- docker/build-push-action@v5
- softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/release.yml:16`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes $CHANGELOG (derived from git commit messages via `git log`) to $GITHUB_OUTPUT without sanitization. Git commit messages are attacker-controlled (a PR author can craft commit messages containing newlines or special sequences) and are written directly with `echo "$CHANGELOG" >> $GITHUB_OUTPUT`. This allows injection of arbitrary key=value pairs into GITHUB_OUTPUT. The required sanitization step (`printf '%s' "$CHANGELOG" | tr -d '\n\r'`) is absent.

Locations:

- `.github/workflows/release.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** missing-permissions, unpinned-uses, github-env-injection

**Notes:**

1. missing-permissions (ci.yml): Added top-level `permissions: contents: read` block to ci.yml.
2. unpinned-uses: Pinned all action references to full 40-character commit SHAs with tag comments:
   - ci.yml: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (×5)
   - code-review.yml: actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 (×5); sulthonzh/code-reviewer@main → @4546aa400043740f47f0023b54663fdb592b253f (×8)
   - release.yml: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (×2); docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130; docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f; docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 (×2); docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051; docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25; softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844
3. github-env-injection (release.yml): The 'Generate changelog' step now sanitizes CHANGELOG using `printf '%s' "$CHANGELOG" | tr -d '\r'` before writing to GITHUB_OUTPUT, and also sanitizes the TAG value. The heredoc delimiter (EOF) approach is preserved but carriage returns are stripped to prevent injection.

