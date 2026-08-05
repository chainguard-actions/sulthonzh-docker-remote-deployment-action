<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.68

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.68** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its 5 jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. This means the workflow runs with the default, potentially over-broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/ci.yml:1`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised.

ci.yml: `actions/checkout@v4` (×5 steps)

code-review.yml: `actions/checkout@v6` (×5 steps), `sulthonzh/code-reviewer@main` (×7 steps — branch ref is especially dangerous)

release.yml: `actions/checkout@v4` (×2), `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, `softprops/action-gh-release@v1`

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:43`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:32`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:47`
- `.github/workflows/code-review.yml:62`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:77`
- `.github/workflows/code-review.yml:82`
- `.github/workflows/code-review.yml:95`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/code-review.yml:107`
- `.github/workflows/code-review.yml:113`
- `.github/workflows/release.yml:18`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:33`
- `.github/workflows/release.yml:39`
- `.github/workflows/release.yml:50`
- `.github/workflows/release.yml:62`
- `.github/workflows/release.yml:81`

### github-env-injection (severity: high)

In release.yml, the 'Generate changelog' step writes two unsanitized values to $GITHUB_OUTPUT without applying the required `printf '%s' ... | tr -d '\n\r'` sanitization:

1. `$TAG` (derived from `GITHUB_REF`) is written via `echo "tag=$TAG" >> $GITHUB_OUTPUT` without sanitization. Although GITHUB_REF is GitHub-controlled, it is still an inherited env var that must be sanitized before writing to special environment files.

2. `$CHANGELOG` is populated from `git log` commit messages (`git log ... --pretty=format:"- %s (%h)"`). Commit messages are attacker-controlled (any contributor can craft a commit message containing newlines or other injection characters) and are written directly to $GITHUB_OUTPUT via `echo "$CHANGELOG" >> $GITHUB_OUTPUT` without any sanitization. A newline in a commit message can inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting outputs consumed by downstream steps.

Locations:

- `.github/workflows/release.yml:68`
- `.github/workflows/release.yml:76`
- `.github/workflows/release.yml:77`
- `.github/workflows/release.yml:78`

## Iteration Notes

### Iteration 1

**Fixes applied:** missing-permissions, unpinned-uses, github-env-injection

**Notes:**

Fixed all three findings across three workflow files:

1. missing-permissions (ci.yml): Added top-level `permissions: contents: read` block to ci.yml. The other two files already had permissions defined.

2. unpinned-uses: Pinned all action references to full 40-char SHA digests with tag comments:
   - ci.yml: actions/checkout@v4 → @11d5960a... (×5)
   - code-review.yml: actions/checkout@v6 → @d23441a... (×5), sulthonzh/code-reviewer@main → @d882af6... (×7)
   - release.yml: actions/checkout@v4 → @11d5960a... (×2), docker/setup-qemu-action@v3 → @c7c5346..., docker/setup-buildx-action@v3 → @8d2750c..., docker/login-action@v3 → @c94ce9f... (×2), docker/metadata-action@v5 → @c299e40..., docker/build-push-action@v5 → @ca052bb..., softprops/action-gh-release@v1 → @de2c0eb...

3. github-env-injection (release.yml): Moved github.ref into step env block; sanitized TAG with `printf | tr -d '\n\r'`; replaced static EOF heredoc delimiter with a random hex value (openssl rand -hex 16) to prevent injection via crafted commit messages containing 'EOF' on its own line.

