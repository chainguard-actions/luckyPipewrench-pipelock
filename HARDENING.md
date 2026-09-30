<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the shell variable VERSION is derived from the env var PIPELOCK_VERSION, which is set to ${{ inputs.version }} (caller-controlled input). It is written unsanitized to $GITHUB_OUTPUT via: echo "version=${VERSION}" >> "$GITHUB_OUTPUT". A value containing a newline character would inject additional key=value pairs into GITHUB_OUTPUT. The fix is to sanitize first: safe=$(printf '%s' "$VERSION" | tr -d '\n\r') and then echo "version=${safe}" >> "$GITHUB_OUTPUT".

Locations:

- `action.yml:160`

### github-env-injection (severity: high)

In the 'Run audit' step, the shell variable CONFIG is derived from the env var PIPELOCK_CONFIG, which is set to ${{ inputs.config }} (caller-controlled input). It is written unsanitized to $GITHUB_OUTPUT via: echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT". A value containing a newline character would inject additional key=value pairs into GITHUB_OUTPUT. The fix is to sanitize first: safe=$(printf '%s' "$CONFIG" | tr -d '\n\r') and then echo "config_path=${safe}" >> "$GITHUB_OUTPUT".

Locations:

- `action.yml:243`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line ~160): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and changed `echo "version=${VERSION}"` to `echo "version=${safe_version}"` before writing to $GITHUB_OUTPUT.
2. 'Run audit' step (line ~243): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` and changed `echo "config_path=${CONFIG}"` to `echo "config_path=${safe_config}"` before writing to $GITHUB_OUTPUT.
Both caller-controlled inputs (inputs.version and inputs.config) are now sanitized to strip newline/carriage-return characters before being written to GITHUB_OUTPUT, preventing injection of additional key=value pairs.

