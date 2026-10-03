<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.6.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Download Pipelock' step writes the VERSION variable (derived from inputs.version via the PIPELOCK_VERSION env var) to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. An attacker-controlled input containing newlines could inject arbitrary workflow output variables. The required sanitization step (`printf '%s' "$VERSION" | tr -d '\n\r'`) is missing before the write.

Locations:

- `action.yml:148`

### github-env-injection (severity: high)

The 'Run audit' step writes the CONFIG variable (derived from inputs.config via the PIPELOCK_CONFIG env var) to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker-controlled input containing newlines could inject arbitrary workflow output variables. The required sanitization step (`printf '%s' "$CONFIG" | tr -d '\n\r'`) is missing before the write.

Locations:

- `action.yml:230`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection, github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. Line ~148 (Download Pipelock step): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing `echo "version=${safe_version}" >> "$GITHUB_OUTPUT"` to sanitize the VERSION variable derived from inputs.version.
2. Line ~230 (Run audit step): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing `echo "config_path=${safe_config}" >> "$GITHUB_OUTPUT"` to sanitize the CONFIG variable derived from inputs.config.
Both sanitizations strip newline and carriage return characters to prevent injection of arbitrary workflow output variables via attacker-controlled inputs.

