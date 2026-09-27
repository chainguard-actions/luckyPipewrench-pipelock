<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the shell variable VERSION is derived from inputs.version (via the PIPELOCK_VERSION env var, which is set to ${{ inputs.version }}). It is written directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' "$VERSION" | tr -d '\n\r'): `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. A caller supplying a version string containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning downstream step outputs.

Locations:

- `action.yml:148`

### github-env-injection (severity: high)

In the 'Run audit' step, the shell variable CONFIG is derived from inputs.config (via the PIPELOCK_CONFIG env var, which is set to ${{ inputs.config }}). It is written directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' "$CONFIG" | tr -d '\n\r'): `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. A caller supplying a config path containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, poisoning downstream step outputs.

Locations:

- `action.yml:231`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. Line ~148 ('Download Pipelock' step): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and changed the GITHUB_OUTPUT write to use `safe_version` instead of `VERSION`.
2. Line ~231 ('Run audit' step): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` and changed the GITHUB_OUTPUT write to use `safe_config` instead of `CONFIG`.
Both variables are derived from user-controlled inputs (inputs.version and inputs.config respectively), so stripping newlines prevents injection of arbitrary key=value pairs into GITHUB_OUTPUT.

