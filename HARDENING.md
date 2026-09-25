<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the shell variable VERSION is derived from the env var PIPELOCK_VERSION which is set from inputs.version (${{ inputs.version }}). This user-controlled value is written directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r'): `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. An attacker-supplied version string containing newline characters could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting subsequent step outputs.

Locations:

- `action.yml:155`

### github-env-injection (severity: high)

In the 'Run audit' step, the shell variable CONFIG is derived from the env var PIPELOCK_CONFIG which is set from inputs.config (${{ inputs.config }}). This user-controlled value is written directly to $GITHUB_OUTPUT without the required sanitization step (printf '%s' ... | tr -d '\n\r'): `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker-supplied config path containing newline characters could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:230`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line 155): Added sanitization of VERSION before writing to GITHUB_OUTPUT: `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` then `echo "version=${safe_version}" >> "$GITHUB_OUTPUT"`.
2. 'Run audit' step (line 230): Added sanitization of CONFIG before writing to GITHUB_OUTPUT: `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` then `echo "config_path=${safe_config}" >> "$GITHUB_OUTPUT"`. Both fixes strip newline and carriage return characters from user-controlled values before writing them to GITHUB_OUTPUT, preventing newline injection attacks.

