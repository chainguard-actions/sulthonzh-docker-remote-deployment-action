<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.74

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.74** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

ci.yml uses `actions/checkout@v4` (a mutable tag, not a pinned SHA) in all 5 job steps. If the tag is moved or the action is compromised, the workflow will silently execute different code. All `uses:` references should be pinned to a full 40-character commit SHA.

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:31`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:49`

### unpinned-uses (severity: high)

code-review.yml uses `actions/checkout@v6` (a mutable tag, not a pinned SHA) in 5 job steps. Note that `@v6` does not exist for `actions/checkout` at the time of writing, making this reference additionally suspect. All `uses:` references should be pinned to a full 40-character commit SHA.

Locations:

- `.github/workflows/code-review.yml:18`
- `.github/workflows/code-review.yml:35`
- `.github/workflows/code-review.yml:57`
- `.github/workflows/code-review.yml:79`
- `.github/workflows/code-review.yml:91`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all 10 unpinned `actions/checkout` references across two workflow files to the full commit SHA `11d5960a326750d5838078e36cf38b85af677262` (actions/checkout v4). In ci.yml, 5 occurrences of `@v4` were replaced. In code-review.yml, 5 occurrences of `@v6` (a non-existent tag) were replaced with the v4 SHA, which is the latest valid version of actions/checkout. All references now use the format `actions/checkout@<sha> # v4` for readability.

