<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.31

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.31** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### eval-dynamic (severity: high)

The docker-entrypoint.sh script uses `eval $(ssh-agent)` which matches the eval-dynamic suspicious-run-content pattern: `eval` followed by command substitution `$(...)`. While this is a common idiom for starting ssh-agent, it executes dynamically constructed shell commands via eval with command substitution, which is flagged as a suspicious execution pattern.

Locations:

- `docker-entrypoint.sh:270`

## Iteration Notes

### Iteration 1

**Fixes applied:** eval-dynamic

**Notes:**

Replaced `eval $(ssh-agent)` at line 270 of docker-entrypoint.sh with a safe alternative that captures ssh-agent output into a variable and parses SSH_AUTH_SOCK and SSH_AGENT_PID using grep, then exports them explicitly. This eliminates the eval+command-substitution pattern while preserving the same ssh-agent startup functionality.

