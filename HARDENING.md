<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Run audit' step, the env var CONFIG is set from inputs.config (via PIPELOCK_CONFIG: ${{ inputs.config }}), which is user-controlled. The value is then written directly to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker can inject newlines into the inputs.config value to poison subsequent GITHUB_OUTPUT entries. The required sanitization step (`safe=$(printf '%s' "$CONFIG" | tr -d '\n\r')`) is missing before the write.

Locations:

- `action.yml:196`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in the 'Run audit' step of action.yml. The user-controlled CONFIG variable (from inputs.config via PIPELOCK_CONFIG env var) was being written directly to $GITHUB_OUTPUT without sanitization. Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before the write, and changed the echo to use `safe_config` instead of `CONFIG`. This strips all newline and carriage return characters from the user-controlled value, preventing newline injection attacks that could poison subsequent GITHUB_OUTPUT entries.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed the github-env-injection finding in the 'Download Pipelock' step of hardened/action/action.yml. Added sanitization before writing the version output to GITHUB_OUTPUT: replaced `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"` with `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r'); echo "version=${safe_version}" >> "$GITHUB_OUTPUT"`. This prevents a crafted version string containing newline characters from injecting additional key=value pairs into GITHUB_OUTPUT. The fix is consistent with the existing sanitization pattern already used for the config_path output in the same file.

