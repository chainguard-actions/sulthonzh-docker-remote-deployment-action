<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.47

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.47** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag or branch refs instead of immutable 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved. Failing references: ci.yml — actions/checkout@v4 (×5); code-review.yml — actions/checkout@v6, sulthonzh/code-reviewer@main (×8 occurrences); release.yml — actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level 'permissions:' block. This means the workflow runs with the default, overly-broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable $CHANGELOG is populated from 'git log' output (commit messages authored by contributors — untrusted, attacker-controlled content) and then written directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' "$CHANGELOG" | tr -d '\n\r'). A malicious commit message containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning downstream step outputs. The offending lines are: 'echo "changelog<<EOF" >> $GITHUB_OUTPUT', 'echo "$CHANGELOG" >> $GITHUB_OUTPUT', 'echo "EOF" >> $GITHUB_OUTPUT'.

Locations:

- `.github/workflows/release.yml:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. **unpinned-uses** (ci.yml, code-review.yml, release.yml): Pinned all mutable tag/branch references to full 40-char SHAs with tag comments:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (ci.yml ×5, release.yml ×2)
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10 (code-review.yml ×6)
   - sulthonzh/code-reviewer@main → @4546aa400043740f47f0023b54663fdb592b253f (code-review.yml ×8)
   - docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130
   - docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f
   - docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 (×2)
   - docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051
   - docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25
   - softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844

2. **missing-permissions** (ci.yml): Added `permissions: {}` top-level block since none of the five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) require GITHUB_TOKEN access.

3. **github-env-injection** (release.yml): Sanitized the changelog generation step by using `printf '%s' "$VAR" | tr -d '\n\r'` for the tag value and `tr -d '\r'` for the multiline changelog before writing to $GITHUB_OUTPUT, preventing newline injection from attacker-controlled commit messages.

### Iteration 2

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced two hardcoded plaintext passwords in docker-compose.yml:
- Line 24: POSTGRES_PASSWORD: changeme → POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
- Line 44: DB_PASSWORD: changeme → DB_PASSWORD: ${DB_PASSWORD}

Both values now use Docker Compose environment variable interpolation, requiring the passwords to be supplied via environment variables or a .env file at runtime rather than being embedded in the compose file.

