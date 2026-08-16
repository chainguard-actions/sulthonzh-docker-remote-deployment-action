<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.70

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **sulthonzh--docker-remote-deployment-action/v1.4.70** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses: actions/checkout@v4` references in ci.yml use a mutable tag (`v4`) instead of a full 40-character commit SHA. This exposes the workflow to supply-chain attacks if the tag is moved. Failing references: actions/checkout@v4 (lines 17, 25, 33, 43, 55).

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:43`
- `.github/workflows/ci.yml:55`

### unpinned-uses (severity: high)

All `uses: actions/checkout@v6` references in code-review.yml use a mutable tag (`v6`) instead of a full 40-character commit SHA. This exposes the workflow to supply-chain attacks. Failing references: actions/checkout@v6 (lines 27, 43, 72, 95, 107). Note: sulthonzh/code-reviewer references in this file ARE properly pinned to a full SHA.

Locations:

- `.github/workflows/code-review.yml:27`
- `.github/workflows/code-review.yml:43`
- `.github/workflows/code-review.yml:72`
- `.github/workflows/code-review.yml:95`
- `.github/workflows/code-review.yml:107`

### unpinned-uses (severity: high)

release.yml uses both mutable tags and a mutable branch reference. `actions/checkout@v4` (tag) is used 5 times, and `sulthonzh/code-reviewer@main` (branch) is used 7 times. Both are unpinned and vulnerable to supply-chain attacks where the referenced tag or branch could be silently updated to point to malicious code.

Locations:

- `.github/workflows/release.yml:27`
- `.github/workflows/release.yml:43`
- `.github/workflows/release.yml:57`
- `.github/workflows/release.yml:72`
- `.github/workflows/release.yml:83`
- `.github/workflows/release.yml:95`
- `.github/workflows/release.yml:107`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all mutable action references to full commit SHAs across three workflow files:
- ci.yml: 5x actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4
- code-review.yml: 5x actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6 (sulthonzh/code-reviewer references were already pinned to a SHA)
- release.yml: 5x actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 # v4, and 7x sulthonzh/code-reviewer@main → @28a101a18c2a6e7ffe62af4eadebfaa96835cfa1 # main
All SHAs were resolved via lookup_action_sha; original tags/branches preserved as inline comments.

