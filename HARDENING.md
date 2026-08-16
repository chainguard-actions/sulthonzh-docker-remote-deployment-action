<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.71

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.71** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use `actions/checkout` pinned to a mutable version tag rather than a full 40-character commit SHA. This means a compromised or altered tag could silently substitute malicious code into the workflow.

- `.github/workflows/ci.yml`: `actions/checkout@v4` used in all 5 job steps (lines 17, 24, 31, 38, 47)
- `.github/workflows/code-review.yml`: `actions/checkout@v6` used in 5 job steps (lines 28, 46, 79, 100, 112)
- `.github/workflows/release.yml`: `actions/checkout@v4` used in 5 job steps (lines 28, 46, 79, 100, 112)

All `uses:` references should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:38`
- `.github/workflows/ci.yml:47`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:100`
- `.github/workflows/code-review.yml:112`
- `.github/workflows/release.yml:28`
- `.github/workflows/release.yml:46`
- `.github/workflows/release.yml:79`
- `.github/workflows/release.yml:100`
- `.github/workflows/release.yml:112`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all 15 unpinned `actions/checkout` references across three workflow files:
- `.github/workflows/ci.yml`: 5 occurrences of `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `.github/workflows/code-review.yml`: 5 occurrences of `actions/checkout@v6` → `actions/checkout@d23441a48e516b6c34aea4fa41551a30e30af803 # v6`
- `.github/workflows/release.yml`: 5 occurrences of `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`

SHAs were resolved using lookup_action_sha. The original tag is preserved as a comment for readability.

