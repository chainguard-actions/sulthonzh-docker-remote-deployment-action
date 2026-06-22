<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.14

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.14** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### suspicious-run-content (severity: high)

The file docker-entrypoint.sh contains `eval $(ssh-agent)` which matches the eval-dynamic pattern (eval followed by command substitution `$()`). While `eval $(ssh-agent)` is a well-known idiom for initializing the SSH agent environment, it technically matches the suspicious pattern `eval\s+[\x60$]` defined by the eval-dynamic sub-check. An attacker who can influence the output of `ssh-agent` (e.g., via PATH manipulation) could cause arbitrary code execution through this eval.

Locations:

- `docker-entrypoint.sh:155`

## Iteration Notes

### Iteration 1

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` at line 155 of docker-entrypoint.sh with a safe alternative that captures ssh-agent output into a variable, then extracts SSH_AUTH_SOCK and SSH_AGENT_PID using `grep -oP` with lookbehind patterns, and exports them explicitly. This eliminates the eval-dynamic pattern while preserving the SSH agent initialization functionality.

