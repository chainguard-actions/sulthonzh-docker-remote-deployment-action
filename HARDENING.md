<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.54

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.54** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of immutable 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced action is compromised or its tag is moved.

.github/workflows/ci.yml: actions/checkout@v4 (×5)
.github/workflows/code-review.yml: actions/checkout@v6 (×5), sulthonzh/code-reviewer@main (×8)
.github/workflows/release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:35`
- `.github/workflows/ci.yml:46`
- `.github/workflows/code-review.yml:20`
- `.github/workflows/code-review.yml:35`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:55`
- `.github/workflows/code-review.yml:67`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:88`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:31`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:48`
- `.github/workflows/release.yml:60`
- `.github/workflows/release.yml:75`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and none of its jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the default repository token permissions, which may be overly broad (write access to contents, packages, etc.).

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes the `$CHANGELOG` variable — populated from `git log` output, which includes untrusted commit messages authored by external contributors — directly to `$GITHUB_OUTPUT` without sanitization. The heredoc pattern used (`echo "changelog<<EOF" >> $GITHUB_OUTPUT; echo "$CHANGELOG" >> $GITHUB_OUTPUT; echo "EOF" >> $GITHUB_OUTPUT`) is vulnerable: a commit message containing a newline followed by the literal string `EOF` would prematurely terminate the heredoc and allow injection of arbitrary key=value pairs into `$GITHUB_OUTPUT`. The required sanitization step (`printf '%s' "$CHANGELOG" | tr -d '\n\r'`) is absent.

Locations:

- `.github/workflows/release.yml:72`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across three workflow files:

1. unpinned-uses: Pinned all action references to full 40-char commit SHAs with original tag as comment. ci.yml: actions/checkout@v4→SHA (×5). code-review.yml: actions/checkout@v6→SHA (×5), sulthonzh/code-reviewer@main→SHA (×8). release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (×2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1 — all pinned.

2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml (the minimum needed for checkout-only jobs).

3. github-env-injection: Rewrote the 'Generate changelog' step in release.yml to sanitize the tag (tr -d newlines), strip any line that is exactly 'EOF' from git log output (grep -v '^EOF$'), and use a unique hard-to-guess delimiter 'CHANGELOG_DELIM' instead of 'EOF' for the multiline heredoc written to $GITHUB_OUTPUT, preventing heredoc injection attacks via crafted commit messages.

