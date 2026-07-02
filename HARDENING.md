<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.32

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.32** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### suspicious-run-content (severity: high)

eval-dynamic: The docker-entrypoint.sh script uses `eval $(ssh-agent)`, which matches the eval-dynamic pattern — eval with command substitution ($()). This pattern is used to obfuscate what code is being executed and is flagged regardless of whether the substituted command is a known binary.

Locations:

- `docker-entrypoint.sh:196`

## Iteration Notes

### Iteration 1

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced `eval $(ssh-agent)` at line 196 of docker-entrypoint.sh with a safe alternative that explicitly parses the ssh-agent output. The new code runs `ssh-agent -s`, captures its output into a variable, then uses `grep -oP` with lookbehind patterns to extract SSH_AUTH_SOCK and SSH_AGENT_PID values and exports them directly — achieving the same result without using eval with command substitution.

