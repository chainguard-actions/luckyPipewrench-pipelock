<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the user-controlled input `inputs.version` is mapped to the env var `PIPELOCK_VERSION` and then assigned to `VERSION` inside the run script. The value is written to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`): `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. An attacker supplying a version string containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting subsequent step outputs.

Locations:

- `action.yml:130`

### github-env-injection (severity: high)

In the 'Run audit' step, the user-controlled input `inputs.config` is mapped to the env var `PIPELOCK_CONFIG` and then assigned to `CONFIG` inside the run script. The value is written to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`): `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker supplying a config path containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:200`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line ~130): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the raw `echo "version=${VERSION}"` with `echo "version=${safe_version}"`.
2. 'Run audit' step (line ~200): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the raw `echo "config_path=${CONFIG}"` with `echo "config_path=${safe_config}"`.
Both user-controlled values are now stripped of newline characters before being written to $GITHUB_OUTPUT, preventing newline injection attacks.

