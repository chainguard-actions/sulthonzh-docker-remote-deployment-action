<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action--/v1.4.39

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action--/v1.4.39** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use mutable tag or branch refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced action is compromised or the tag is moved.

ci.yml: actions/checkout@v4 (x5)
code-review.yml: actions/checkout@v6 (x5), sulthonzh/code-reviewer@main (x7)
release.yml: actions/checkout@v4 (x2), docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 (x2), docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/release.yml:14`

### missing-permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its five jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define job-level permissions. This means the workflow runs with the default, overly broad token permissions.

Locations:

- `.github/workflows/ci.yml:1`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes $TAG (derived from GITHUB_REF) and $CHANGELOG (derived from git log commit messages, which can be attacker-controlled) to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r'). A crafted commit message containing newlines could inject additional key=value pairs into GITHUB_OUTPUT, potentially influencing downstream steps. Offending lines: 'echo "tag=$TAG" >> $GITHUB_OUTPUT' and 'echo "$CHANGELOG" >> $GITHUB_OUTPUT'.

Locations:

- `.github/workflows/release.yml:57`

### suspicious-run-content (severity: high)

docker-entrypoint.sh contains 'eval $(ssh-agent)' which matches the eval-dynamic pattern (eval followed by command substitution $(...)). The pattern eval\s+[$] is matched. While this is a common SSH agent initialization idiom, it executes the runtime output of ssh-agent via eval.

Locations:

- `docker-entrypoint.sh:183`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action refs to full 40-char SHAs in ci.yml (actions/checkout@v4 x5), code-review.yml (actions/checkout@v6 x5, sulthonzh/code-reviewer@main x7), and release.yml (actions/checkout@v4 x2, docker/setup-qemu-action@v3, docker/setup-buildx-action@v3, docker/login-action@v3 x2, docker/metadata-action@v5, docker/build-push-action@v5, softprops/action-gh-release@v1). 2. missing-permissions: Added top-level 'permissions: contents: read' block to ci.yml. 3. github-env-injection: Fixed the Generate changelog step in release.yml to sanitize TAG with printf+tr before writing to GITHUB_OUTPUT, and sanitize CHANGELOG (strip carriage returns) before writing via heredoc. 4. suspicious-run-content: Replaced 'eval $(ssh-agent)' in docker-entrypoint.sh with explicit parsing of ssh-agent -s output using grep -oP to extract SSH_AUTH_SOCK and SSH_AGENT_PID without eval.

