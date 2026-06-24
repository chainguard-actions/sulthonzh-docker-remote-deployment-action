<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.19

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.19** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### suspicious-run-content (severity: high)

eval-dynamic: The docker-entrypoint.sh script uses `eval $(ssh-agent)` which matches the eval-dynamic pattern — eval with command substitution ($()). Per the check rules, `eval $(...)` is a failing pattern regardless of whether the substituted command is trusted. Matching pattern: `eval\s+[\x60$]`.

Locations:

- `docker-entrypoint.sh:167`

### hardcoded-credentials (severity: high)

The docker-compose.yml file shipped with the action contains a hardcoded literal password: `DB_PASSWORD: mypassword`. This is a non-expression literal value assigned to a key containing 'password', matching the pattern `(?i)(password|secret|token|api_key|aws_secret)\s*[=:]\s*[A-Za-z0-9][A-Za-z0-9+/=_\-]{7,}`.

Locations:

- `docker-compose.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** suspicious-run-content, hardcoded-credentials

**Notes:**

1. docker-entrypoint.sh line 167: Replaced `eval $(ssh-agent)` with a safe alternative — ssh-agent is started with `-s` flag, its output is captured in a variable, then SSH_AUTH_SOCK and SSH_AGENT_PID are extracted using grep with lookbehind patterns and exported. This eliminates the eval-with-command-substitution pattern while preserving full functionality. 2. docker-compose.yml line 46: Replaced the hardcoded literal `DB_PASSWORD: mypassword` with `DB_PASSWORD: ${DB_PASSWORD:?DB_PASSWORD environment variable must be set}` — this uses Docker Compose's variable substitution with a mandatory-variable syntax that will fail with a descriptive error if the environment variable is not set at runtime, forcing users to supply the password securely.

