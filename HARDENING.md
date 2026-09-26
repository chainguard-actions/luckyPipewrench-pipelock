<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the variable VERSION (sourced from PIPELOCK_VERSION, which is set from the untrusted input `${{ inputs.version }}`) is written directly to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. An attacker-controlled value containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting other outputs. The required sanitization (`safe=$(printf '%s' "$VERSION" | tr -d '\n\r')`) is absent.

Locations:

- `action.yml:148`

### github-env-injection (severity: high)

In the 'Run audit' step, the variable CONFIG (sourced from PIPELOCK_CONFIG, which is set from the untrusted input `${{ inputs.config }}`) is written directly to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker-controlled value containing newlines could inject arbitrary key=value pairs into GITHUB_OUTPUT. The required sanitization (`safe=$(printf '%s' "$CONFIG" | tr -d '\n\r')`) is absent.

Locations:

- `action.yml:220`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line ~148): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the unsanitized `echo "version=${VERSION}"` with `echo "version=${safe_version}"`.
2. 'Run audit' step (line ~220): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the unsanitized `echo "config_path=${CONFIG}"` with `echo "config_path=${safe_config}"`.
Both values are sourced from untrusted user inputs (inputs.version and inputs.config respectively), and the sanitization strips newline/carriage-return characters that could be used to inject additional key=value pairs into GITHUB_OUTPUT.

