<!-- markdownlint-disable -->

# Hardening Report: sulthonzh--docker-remote-deployment-action/v1.4.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **sulthonzh--docker-remote-deployment-action/v1.4.15** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

The file docker-compose.yml contains a hardcoded plaintext password: `DB_PASSWORD: mypassword`. This literal credential value is embedded directly in the file rather than being sourced from a secret or environment variable at runtime. Anyone with read access to the repository can obtain this password.

Locations:

- `docker-compose.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced the hardcoded plaintext password `DB_PASSWORD: mypassword` in docker-compose.yml line 47 with `DB_PASSWORD: ${DB_PASSWORD}`. Docker Compose will now interpolate the value from the runtime environment (shell env var or .env file), eliminating the embedded credential. No other findings were present.

### Iteration 1

**Fixes applied:** suspicious-run-content

**Notes:**

Replaced the dangerous `eval "${DEPLOYMENT_COMMAND} ${INPUT_ARGS}"` pattern with safe bash array execution. Key changes to docker-entrypoint.sh: (1) Replaced the string-based DEPLOYMENT_COMMAND variable with a bash array DEPLOYMENT_CMD_ARRAY, building each argument as a separate array element to prevent shell injection; (2) Removed the `eval` call entirely, replacing it with `"${DEPLOYMENT_CMD_ARRAY[@]}" "${INPUT_ARGS_ARRAY[@]}"` direct array expansion; (3) Split INPUT_ARGS into an array using `IFS=' ' read -r -a INPUT_ARGS_ARRAY <<< "$INPUT_ARGS"` to safely handle multiple arguments without eval; (4) Updated all execute_ssh calls to use `"${DEPLOYMENT_CMD_ARRAY[@]}"` instead of the old string variable. The `eval $(ssh-agent)` line was intentionally left unchanged as it is a standard, safe idiom for starting the SSH agent with no user-controlled input.

