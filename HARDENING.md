<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.73

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.73** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files use mutable version tags instead of full 40-character commit SHAs for `actions/checkout`, making the workflows vulnerable to supply-chain attacks if the tag is moved.

- `.github/workflows/ci.yml`: `uses: actions/checkout@v4` appears on lines 17, 25, 33, 42, and 52.
- `.github/workflows/code-review.yml`: `uses: actions/checkout@v6` appears on lines 28, 56, 74, 99, and 113.

Note: The `sulthonzh/code-reviewer@c9301021012f94d42f1954590498e96b09864e72` references in `code-review.yml` are correctly pinned to a full SHA and are not a finding.

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:52`
- `.github/workflows/code-review.yml:28`
- `.github/workflows/code-review.yml:56`
- `.github/workflows/code-review.yml:74`
- `.github/workflows/code-review.yml:99`
- `.github/workflows/code-review.yml:113`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all 10 unpinned `actions/checkout` references to full commit SHAs:
- `.github/workflows/ci.yml`: 5 occurrences of `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `.github/workflows/code-review.yml`: 5 occurrences of `actions/checkout@v6` → `actions/checkout@d23441a48e516b6c34aea4fa41551a30e30af803 # v6`
The already-pinned `sulthonzh/code-reviewer@c9301021012f94d42f1954590498e96b09864e72` references were left unchanged.

