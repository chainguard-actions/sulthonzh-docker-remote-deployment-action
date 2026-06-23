<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.18

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.18** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### eval-dynamic (severity: high)

In docker-entrypoint.sh, the line `eval $(ssh-agent)` matches the eval-dynamic suspicious-run-content check pattern (`eval\s+[\x60$]`). This uses eval with command substitution ($(...)), which is the flagged pattern for dynamically constructing and executing shell commands. While `eval $(ssh-agent)` is a well-known SSH agent initialization idiom, it is indistinguishable from the dangerous pattern by static analysis.

Locations:

- `docker-entrypoint.sh:160`

## Iteration Notes

### Iteration 1

**Fixes applied:** eval-dynamic

**Notes:**

Replaced `eval $(ssh-agent)` on line 160 of docker-entrypoint.sh with a safe alternative that captures ssh-agent output into a variable and extracts SSH_AUTH_SOCK and SSH_AGENT_PID using grep -oP with PCRE lookbehind patterns, then exports them explicitly. This avoids the eval+command-substitution pattern flagged by the eval-dynamic check while preserving identical SSH agent initialization behavior.

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced the hardcoded literal `DB_PASSWORD: mypassword` in docker-compose.yml (line 44) with `DB_PASSWORD: ${DB_PASSWORD}`. Docker Compose will now read the password from the host environment at runtime, preventing the credential from being stored in the repository.

