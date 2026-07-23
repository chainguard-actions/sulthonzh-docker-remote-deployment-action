<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.51

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.51** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference actions using mutable tags or branch names instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks. ci.yml uses actions/checkout@v4 (5 steps). code-review.yml uses actions/checkout@v6 (4 steps) and sulthonzh/code-reviewer@main (8 steps, including auto-merge and auto-release steps with write permissions). release.yml uses actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, and softprops/action-gh-release@v1.

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:42`
- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:24`
- `.github/workflows/code-review.yml:38`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:68`
- `.github/workflows/code-review.yml:76`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:90`
- `.github/workflows/code-review.yml:97`
- `.github/workflows/code-review.yml:104`
- `.github/workflows/code-review.yml:112`
- `.github/workflows/release.yml:13`
- `.github/workflows/release.yml:17`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`
- `.github/workflows/release.yml:31`
- `.github/workflows/release.yml:37`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:63`
- `.github/workflows/release.yml:88`

### missing-permissions (severity: medium)

ci.yml has no top-level permissions: key and none of its 5 jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level permissions: block. The workflow therefore runs with default token permissions, which may be overly broad depending on repository settings.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In the Generate changelog step of release.yml, the variable $CHANGELOG is populated from git log commit messages, which are attacker-controlled (any contributor can craft a commit message with embedded newlines). This value is written directly to $GITHUB_OUTPUT via 'echo "$CHANGELOG" >> $GITHUB_OUTPUT' without the required sanitization step (printf '%s' "$CHANGELOG" | tr -d '\n\r'). A malicious commit message containing a newline followed by key=value could inject arbitrary entries into GITHUB_OUTPUT, influencing downstream steps such as the Create Release step that consumes ${{ steps.changelog.outputs.changelog }}.

Locations:

- `.github/workflows/release.yml:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

Fixed all three findings across the three workflow files:

1. unpinned-uses: Pinned all action references to full 40-char SHAs with tag comments preserved:
   - ci.yml: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4 (5 occurrences)
   - code-review.yml: actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6 (4 occurrences); sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c # main (8 occurrences)
   - release.yml: actions/checkout@v4, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1 — all pinned to their resolved SHAs

2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml (minimum needed for a read-only CI workflow).

3. github-env-injection: In release.yml's Generate changelog step, sanitized both TAG (via tr -d '\n\r') and CHANGELOG (via tr -d '\r') before writing to $GITHUB_OUTPUT, preventing attacker-controlled commit messages from injecting arbitrary key=value pairs into GITHUB_OUTPUT.

