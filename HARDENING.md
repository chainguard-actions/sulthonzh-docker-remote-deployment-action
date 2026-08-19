<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.15** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references in both workflow files use mutable tag or branch refs instead of immutable 40-character SHA commit pins, making the workflows vulnerable to supply-chain attacks if the referenced action tags are moved or compromised.

.github/workflows/main.yml:
- `actions/checkout@v6`
- `docker/setup-qemu-action@v4`
- `docker/setup-buildx-action@v4`
- `docker/login-action@v4`
- `docker/build-push-action@v7`

.github/workflows/code-review.yml:
- `actions/checkout@v6` (multiple steps)
- `sulthonzh/code-reviewer@main` (multiple steps — branch ref)

Locations:

- `.github/workflows/main.yml:14`
- `.github/workflows/main.yml:17`
- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:24`
- `.github/workflows/main.yml:30`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:26`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:44`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:74`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:91`
- `.github/workflows/code-review.yml:94`
- `.github/workflows/code-review.yml:102`
- `.github/workflows/code-review.yml:108`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key and its only job (`build`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions (which may include write access to contents and packages), violating the principle of least privilege.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references in both workflow files by pinning them to full 40-character commit SHAs (with original tag/branch preserved as inline comments). Specifically: actions/checkout@v6 → SHA d23441a..., docker/setup-qemu-action@v4 → SHA 96fe6ef..., docker/setup-buildx-action@v4 → SHA bb05f3f..., docker/login-action@v4 → SHA af1e73f..., docker/build-push-action@v7 → SHA 53b7df9..., sulthonzh/code-reviewer@main → SHA d882af6... (9 occurrences). Added a top-level `permissions:` block to main.yml with `contents: read` and `packages: write` (minimum needed for checkout + DockerHub push). The code-review.yml already had a permissions block and was left unchanged in that regard.

