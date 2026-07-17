<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.46

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.46** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all three workflow files use mutable tag or branch refs instead of immutable 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved.

Failing references in .github/workflows/ci.yml:
- `actions/checkout@v4` (×5 jobs)

Failing references in .github/workflows/code-review.yml:
- `actions/checkout@v6` (×5 occurrences)
- `sulthonzh/code-reviewer@main` (×7 occurrences — branch ref is especially dangerous)

Failing references in .github/workflows/release.yml:
- `actions/checkout@v4`
- `docker/setup-qemu-action@v3`
- `docker/setup-buildx-action@v3`
- `docker/login-action@v3` (×2)
- `docker/metadata-action@v5`
- `docker/build-push-action@v5`
- `softprops/action-gh-release@v1`

All should be pinned to full SHA digests, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/ci.yml:13`
- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:31`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`
- `.github/workflows/release.yml:26`
- `.github/workflows/release.yml:32`
- `.github/workflows/release.yml:37`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:72`

### missing-permissions (severity: medium)

`ci.yml` has no top-level `permissions:` key and none of its five jobs (`shell-lint`, `dockerfile-lint`, `validate-yaml`, `security-scan`, `build-image`) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). A minimal `permissions: read-all` or specific per-job scopes should be added.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the `create-github-release` job of `release.yml`, the "Generate changelog" step writes two unsanitized, attacker-influenced values to `$GITHUB_OUTPUT` without applying the required `printf '%s' ... | tr -d '\n\r'` sanitization:

1. `TAG=${GITHUB_REF#refs/tags/}` then `echo "tag=$TAG" >> $GITHUB_OUTPUT` — the tag name is part of the git ref and can be crafted by whoever pushes the tag, allowing newline injection into GITHUB_OUTPUT.

2. `CHANGELOG=$(git log ... --pretty=format:"- %s (%h)")` then `echo "$CHANGELOG" >> $GITHUB_OUTPUT` — git commit messages are entirely attacker-controlled (any contributor can craft a commit message containing newlines or `key=value` pairs), enabling injection of arbitrary key-value pairs into the output context.

The correct pattern requires sanitization before each write:
```bash
safe_tag=$(printf '%s' "$TAG" | tr -d '\n\r')
echo "tag=$safe_tag" >> "$GITHUB_OUTPUT"
```

Locations:

- `.github/workflows/release.yml:66`
- `.github/workflows/release.yml:74`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. unpinned-uses: Pinned all `uses:` references to full 40-char SHA digests with tag comments preserved. Covered actions/checkout (v4 and v6), sulthonzh/code-reviewer (main), docker/setup-qemu-action (v3), docker/setup-buildx-action (v3), docker/login-action (v3 ×2), docker/metadata-action (v5), docker/build-push-action (v5), and softprops/action-gh-release (v1).

2. missing-permissions: Added `permissions: contents: read` top-level block to ci.yml. The release.yml already had per-job permissions blocks; code-review.yml already had a top-level permissions block.

3. github-env-injection: In release.yml's 'Generate changelog' step, sanitized the TAG value using `printf '%s' "$TAG" | tr -d '\n\r'` before writing to GITHUB_OUTPUT, and sanitized the CHANGELOG value using `printf '%s' "$CHANGELOG" | tr -d '\r'` before writing via heredoc to GITHUB_OUTPUT. Also quoted `"$GITHUB_OUTPUT"` throughout.

