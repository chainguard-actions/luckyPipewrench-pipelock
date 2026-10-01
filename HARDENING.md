<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.5.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the variable VERSION is derived from $PIPELOCK_VERSION, which is set from the untrusted input ${{ inputs.version }}. VERSION is written to $GITHUB_OUTPUT via `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$VERSION" | tr -d '\n\r'`). An attacker-controlled version string containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting subsequent step outputs.

Locations:

- `action.yml:131`

### github-env-injection (severity: high)

In the 'Run audit' step, the variable CONFIG is derived from $PIPELOCK_CONFIG, which is set from the untrusted input ${{ inputs.config }}. CONFIG is written to $GITHUB_OUTPUT via `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$CONFIG" | tr -d '\n\r'`). An attacker-controlled config path containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:207`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. Line ~131 (Download Pipelock step): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the direct `echo "version=${VERSION}"` with `echo "version=${safe_version}"`.
2. Line ~207 (Run audit step): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the direct `echo "config_path=${CONFIG}"` with `echo "config_path=${safe_config}"`.
Both fixes strip newline and carriage return characters from user-controlled inputs before they are written to $GITHUB_OUTPUT, preventing newline injection attacks that could overwrite subsequent step outputs.

