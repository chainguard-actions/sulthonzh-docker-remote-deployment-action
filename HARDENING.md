<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.55

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.55** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions using mutable version tags or branch names instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if any referenced action is compromised or its tag is moved.

ci.yml: actions/checkout@v4 (x5)

code-review.yml: actions/checkout@v6 (x5), sulthonzh/code-reviewer@main (x7 — especially dangerous as @main is a moving branch ref)

release.yml: actions/checkout@v4 (x2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:42`
- `.github/workflows/code-review.yml:19`
- `.github/workflows/code-review.yml:25`
- `.github/workflows/code-review.yml:40`
- `.github/workflows/code-review.yml:45`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:75`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:93`
- `.github/workflows/code-review.yml:95`
- `.github/workflows/code-review.yml:103`
- `.github/workflows/code-review.yml:110`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:40`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:63`
- `.github/workflows/release.yml:80`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. The workflow therefore runs with GitHub's default token permissions, which include write access to repository contents and packages on push events. This violates the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the 'Generate changelog' step of release.yml, the variable $CHANGELOG is populated from `git log` commit subject lines (attacker-controllable via crafted commit messages) and written directly to $GITHUB_OUTPUT without sanitization: `echo "$CHANGELOG" >> $GITHUB_OUTPUT`. A commit message containing a newline followed by `key=value` could inject additional output variables into $GITHUB_OUTPUT. The static heredoc delimiter `EOF` is also vulnerable: a commit message containing `EOF` on its own line would prematurely terminate the heredoc and allow injection of arbitrary content. The required sanitization (`printf '%s' "$CHANGELOG" | tr -d '\n\r'`) is absent.

Locations:

- `.github/workflows/release.yml:76`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings:

1. unpinned-uses: Pinned all action references to full 40-char commit SHAs in all three workflow files (ci.yml x5, code-review.yml x12, release.yml x9). Original tags preserved as inline comments.

2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml, which had no permissions block at all.

3. github-env-injection: Replaced the vulnerable heredoc pattern in release.yml's 'Generate changelog' step (where a commit message containing 'EOF' could terminate the heredoc prematurely and inject content) with a sanitized single-line approach using `printf '%s' "$CHANGELOG" | tr -d '\r'` before writing to $GITHUB_OUTPUT. Also sanitized the TAG value with `tr -d '\n\r'`.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the GITHUB_OUTPUT injection vulnerability in the 'Generate changelog' step of .github/workflows/release.yml. Changed the sanitization of `safe_changelog` from `tr -d '\r'` (which only removed carriage returns) to `tr -d '\n\r'` (which removes both newlines and carriage returns). This prevents attacker-controlled commit messages containing newline characters from injecting additional key=value pairs into the $GITHUB_OUTPUT file. The fix is consistent with how `safe_tag` is already sanitized in the same step.

