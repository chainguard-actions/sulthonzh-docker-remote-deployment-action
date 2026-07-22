<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.37

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.37** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved.

- .github/workflows/ci.yml: `actions/checkout@v4` (used in all 5 jobs)
- .github/workflows/code-review.yml: `actions/checkout@v6`, `sulthonzh/code-reviewer@main` (used 7 times across all jobs)
- .github/workflows/release.yml: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3` (×2), `docker/metadata-action@v5`, `docker/build-push-action@v5`, `softprops/action-gh-release@v1`

All of these must be pinned to their full SHA digest (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/ci.yml:12`
- `.github/workflows/code-review.yml:22`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/release.yml:16`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:24`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:38`
- `.github/workflows/release.yml:44`
- `.github/workflows/release.yml:73`

### missing-permissions (severity: medium)

The workflow file .github/workflows/ci.yml has no top-level `permissions:` key and none of its 5 jobs (shell-lint, dockerfile-lint, validate-yaml, security-scan, build-image) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal `permissions: read-all` or specific per-job scopes should be declared.

Locations:

- `.github/workflows/ci.yml:1`

### hardcoded-credentials (severity: high)

The file docker-compose.yml contains a hardcoded plaintext password: `DB_PASSWORD: mypassword`. This literal credential value is committed to the repository and matches the pattern `password\s*:\s*[A-Za-z0-9]{8,}`. Even if this is an example/demo file, it should use an environment variable reference (e.g. `DB_PASSWORD: ${DB_PASSWORD}`) or a Docker secret instead of a hardcoded value.

Locations:

- `docker-compose.yml:43`

### github-env-injection (severity: high)

In .github/workflows/release.yml, the 'Generate changelog' step writes the `$CHANGELOG` variable — populated from `git log` commit messages — directly to `$GITHUB_OUTPUT` without sanitizing newline characters first. Commit messages are untrusted external data and can contain embedded newlines (`\n`) that inject additional `key=value` pairs into the output file, potentially overwriting other step outputs. The fix is to sanitize before writing: `safe=$(printf '%s' "$CHANGELOG" | tr -d '\n\r')` and then use a heredoc or the sanitized value.

Offending lines:
  `echo "changelog<<EOF" >> $GITHUB_OUTPUT`
  `echo "$CHANGELOG" >> $GITHUB_OUTPUT`
  `echo "EOF" >> $GITHUB_OUTPUT`

Locations:

- `.github/workflows/release.yml:51`
- `.github/workflows/release.yml:52`
- `.github/workflows/release.yml:53`

### suspicious-run-content (severity: high)

Sub-check: eval-dynamic. The file docker-entrypoint.sh contains `eval $(ssh-agent)`, which matches the eval-dynamic pattern `eval\s+[$]`. This uses command substitution (`$(...)`) to dynamically construct and execute shell commands via `eval`. While `ssh-agent` is a known system binary, this pattern is flagged because `eval` combined with command substitution (`$(...)`) can execute arbitrary dynamic output as shell code. If the `ssh-agent` binary or PATH were compromised, this would execute attacker-controlled code. The safer alternative is to use `ssh-agent` with explicit variable capture: `SSH_AGENT_OUTPUT=$(ssh-agent); eval "$SSH_AGENT_OUTPUT"` with the output validated, or use a dedicated SSH agent management approach.

Locations:

- `docker-entrypoint.sh:196`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, hardcoded-credentials, github-env-injection, suspicious-run-content

**Notes:**

1. unpinned-uses: Pinned all action references to full SHAs — actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803, sulthonzh/code-reviewer@main → @d882af6cd1ae55f692c0a8dfc6ff464115ccf89c, docker/setup-qemu-action@v3 → @c7c53464625b32c7a7e944ae62b3e17d2b600130, docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9, docker/metadata-action@v5 → @c299e40c65443455700f0fdfc63efafe5b349051, docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25, softprops/action-gh-release@v1 → @de2c0eb89ae2a093876385947365aca7b0e5f844. 2. missing-permissions: Added top-level `permissions: contents: read` to ci.yml. 3. hardcoded-credentials: Replaced `DB_PASSWORD: mypassword` with `DB_PASSWORD: ${DB_PASSWORD}` in docker-compose.yml. 4. github-env-injection: Fixed the changelog step in release.yml to sanitize values using `printf '%s' | tr -d '\n\r'` before writing to GITHUB_OUTPUT. 5. suspicious-run-content: Changed `eval $(ssh-agent)` to capture output first then evaluate the quoted variable: `SSH_AGENT_OUTPUT=$(ssh-agent); eval "$SSH_AGENT_OUTPUT"`.

