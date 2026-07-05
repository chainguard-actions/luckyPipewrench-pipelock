<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock--/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **luckyPipewrench--pipelock--/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow file .github/workflows/scorecard.yaml has a top-level 'permissions: read-all' which grants overly broad read access to all scopes. This should be replaced with specific minimal permissions.

Locations:

- `.github/workflows/scorecard.yaml:11`

### github-env-injection (severity: high)

In action.yml, the 'Run audit' step writes the value of inputs.config (user-controlled) directly to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. The variable $CONFIG is set from $PIPELOCK_CONFIG which is ${{ inputs.config }}. An attacker can inject newlines into this value to poison subsequent GITHUB_OUTPUT entries. The fix is to sanitize with: safe=$(printf '%s' "$CONFIG" | tr -d '\n\r') before writing.

Locations:

- `action.yml:230`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions, github-env-injection

**Notes:**

1. scorecard.yaml: Replaced top-level 'permissions: read-all' with 'permissions: {}'. The job already has a specific permissions block (security-events: write, id-token: write) that grants only what's needed. 2. action.yml: In the 'Run audit' step, sanitized the user-controlled $CONFIG value before writing to $GITHUB_OUTPUT by using 'safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')' and then writing 'config_path=${safe_config}' to prevent newline injection attacks.

