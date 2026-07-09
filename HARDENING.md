<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.44

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.44** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag/branch refs instead of pinned SHA digests, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved.

- ci.yml: `actions/checkout@v4` (lines 13, 20, 27, 35, 43)
- code-review.yml: `actions/checkout@v6` (lines 22, 40), `sulthonzh/code-reviewer@main` (lines 26, 44, 55, 68, 74, 80, 86)
- release.yml: `actions/checkout@v4` (lines 14, 50), `docker/setup-qemu-action@v3` (line 18), `docker/setup-buildx-action@v3` (line 22), `docker/login-action@v3` (lines 26, 32), `docker/metadata-action@v5` (line 38), `docker/build-push-action@v5` (line 48), `softprops/action-gh-release@v1` (line 73)

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:74`
- `.github/workflows/code-review.yml:80`
- `.github/workflows/code-review.yml:86`
- `.github/workflows/release.yml:14`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:48`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:73`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and no job-level `permissions:` on any of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image). Without explicit permissions, the workflow runs with the default token permissions which may be overly broad.

Locations:

- `.github/workflows/ci.yml:1`

### hardcoded-credentials (severity: high)

docker-compose.yml contains hardcoded literal passwords: `POSTGRES_PASSWORD: changeme` (line 24) and `DB_PASSWORD: changeme` (line 46). These are example/default credentials committed to the repository and should be replaced with secret references.

Locations:

- `docker-compose.yml:24`
- `docker-compose.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials

**Notes:**

1. unpinned-uses: Pinned all action references to full commit SHAs in all three workflow files: actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10, sulthonzh/code-reviewer@main → @d0c6f9c936438fbd487b575f55f739ba52f4cc37, docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844. 2. missing-permissions: Added `permissions: {}` top-level block to ci.yml. 3. hardcoded-credentials: Replaced hardcoded literal passwords 'changeme' in docker-compose.yml with Docker Compose environment variable interpolation syntax (${POSTGRES_PASSWORD} and ${DB_PASSWORD}), requiring these values to be supplied at runtime via environment variables or a .env file.

