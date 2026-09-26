<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.4.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Run audit' step, the variable CONFIG (derived from PIPELOCK_CONFIG, which is set from inputs.config) is written directly to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker-controlled value for inputs.config containing newline characters could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting other outputs. The required sanitization step (`safe=$(printf '%s' "$CONFIG" | tr -d '\n\r')`) is absent before this write.

Locations:

- `action.yml:196`

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the variable VERSION (derived from PIPELOCK_VERSION, which is set from inputs.version) is written to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. When inputs.version is not 'latest', VERSION is taken directly from the user-supplied input (only stripped of a leading 'v' via parameter expansion). A value containing newline characters could inject arbitrary key=value pairs into GITHUB_OUTPUT. The required sanitization step (`safe=$(printf '%s' "$VERSION" | tr -d '\n\r')`) is absent before this write.

Locations:

- `action.yml:131`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line 131): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the direct `echo "version=${VERSION}"` with `echo "version=${safe_version}"`.
2. 'Run audit' step (line 196): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the direct `echo "config_path=${CONFIG}"` with `echo "config_path=${safe_config}"`.
Both fixes strip newline and carriage return characters from user-controlled input values before writing them to GITHUB_OUTPUT, preventing newline injection attacks that could allow an attacker to inject arbitrary key=value pairs into the output.

