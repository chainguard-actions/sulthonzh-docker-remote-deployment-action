<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.72

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.72** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files use tag-based (non-SHA-pinned) action references, which are vulnerable to supply-chain attacks if the tag is moved or the upstream repository is compromised.

.github/workflows/ci.yml: uses actions/checkout@v4 (tag) in all 5 jobs (lines 17, 25, 33, 42, 52). Pin to a full 40-character commit SHA, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4

.github/workflows/code-review.yml: uses actions/checkout@v6 (tag) in all 5 jobs (lines 29, 46, 73, 96, 109). Pin to a full 40-character commit SHA.

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:52`
- `.github/workflows/code-review.yml:29`
- `.github/workflows/code-review.yml:46`
- `.github/workflows/code-review.yml:73`
- `.github/workflows/code-review.yml:96`
- `.github/workflows/code-review.yml:109`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all 10 unpinned action references across two workflow files:
- .github/workflows/ci.yml: 5 occurrences of `actions/checkout@v4` pinned to SHA `11d5960a326750d5838078e36cf38b85af677262` (# v4)
- .github/workflows/code-review.yml: 5 occurrences of `actions/checkout@v6` pinned to SHA `d23441a48e516b6c34aea4fa41551a30e30af803` (# v6)
All SHAs were resolved via lookup_action_sha. Original tags preserved as inline comments for readability.

