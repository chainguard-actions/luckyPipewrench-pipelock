<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.7.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the shell variable VERSION is derived from the env var PIPELOCK_VERSION which is set from ${{ inputs.version }} (user-controlled). It is written directly to $GITHUB_OUTPUT via `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$VERSION" | tr -d '\n\r'`). A malicious caller can inject newlines into the version input to smuggle arbitrary key=value pairs into the output file, potentially poisoning downstream steps.

Locations:

- `action.yml:157`

### github-env-injection (severity: high)

In the 'Run audit' step, the shell variable CONFIG is derived from the env var PIPELOCK_CONFIG which is set from ${{ inputs.config }} (user-controlled). It is written directly to $GITHUB_OUTPUT via `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$CONFIG" | tr -d '\n\r'`). A malicious caller can inject newlines into the config input to smuggle arbitrary key=value pairs into the output file, potentially poisoning downstream steps that consume the config_path output.

Locations:

- `action.yml:232`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in action.yml:
1. 'Download Pipelock' step (line ~157): Added `safe_version="$(printf '%s' "$VERSION" | tr -d '\n\r')"` before writing `version=${safe_version}` to $GITHUB_OUTPUT. The VERSION variable is derived from user-controlled `inputs.version`.
2. 'Run audit' step (line ~232): Added `safe_config="$(printf '%s' "$CONFIG" | tr -d '\n\r')"` before writing `config_path=${safe_config}` to $GITHUB_OUTPUT. The CONFIG variable is derived from user-controlled `inputs.config`.
Both fixes sanitize the values by stripping newline characters before writing to $GITHUB_OUTPUT, preventing newline injection attacks that could smuggle arbitrary key=value pairs into the output file.

