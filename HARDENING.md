<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.67

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.67** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the tag is moved.

ci.yml: uses: actions/checkout@v4 (appears 5 times across all jobs)

release.yml: uses: actions/checkout@v4, uses: docker/setup-qemu-action@v3, uses: docker/setup-buildx-action@v3, uses: docker/login-action@v3 (×2), uses: docker/metadata-action@v5, uses: docker/build-push-action@v5, uses: softprops/action-gh-release@v1

code-review.yml: uses: actions/checkout@v6 (×2), uses: sulthonzh/code-reviewer@main (×7 across all jobs)

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/release.yml:13`
- `.github/workflows/code-review.yml:20`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. This means the workflow runs with the default, overly broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across three workflow files by resolving each tag/branch to its full 40-character commit SHA (using lookup_action_sha) and appending the original tag as a comment. Added a top-level `permissions: contents: read` block to ci.yml to address the missing-permissions finding. The release.yml and code-review.yml already had permissions blocks at the job or workflow level respectively, so only their action pins needed updating.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in .github/workflows/release.yml:

1. Tag output (line 77): Sanitized $TAG (derived from $GITHUB_REF) using `printf '%s' "$TAG" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT.

2. Changelog output (line 87): (a) Sanitized $CHANGELOG (from git log) using `printf '%s' "$CHANGELOG" | tr -d '\r'` to remove carriage returns. (b) Replaced the static 'EOF' heredoc delimiter with a randomly generated unique delimiter `CHANGELOG_DELIM_$(openssl rand -hex 16)` to prevent an attacker from injecting a line containing exactly 'EOF' in a commit message to break out of the heredoc and inject additional GITHUB_OUTPUT key=value pairs. Used `printf` with the dynamic delimiter for the multiline write.

