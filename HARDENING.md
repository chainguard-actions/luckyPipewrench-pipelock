<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Run audit' step, the shell variable CONFIG (derived from env var PIPELOCK_CONFIG, which is set from inputs.config — a user-controlled composite action input) is written directly to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker who controls the inputs.config value can inject newline characters to poison GITHUB_OUTPUT and set arbitrary output variables. The required sanitization step (`safe=$(printf '%s' "$CONFIG" | tr -d '\n\r')`) is missing before the write.

Locations:

- `action.yml:228`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in the 'Run audit' step of hardened/action/action.yml. The CONFIG variable (derived from user-controlled inputs.config via PIPELOCK_CONFIG env var) was being written directly to $GITHUB_OUTPUT without sanitization. Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before the write, and changed the echo to use `safe_config` instead of `CONFIG`. This strips any newline characters that could be used to inject additional key=value pairs into GITHUB_OUTPUT.

