<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.5.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, two unsanitized values are written to special environment files without applying `printf '%s' ... | tr -d '\n\r'` sanitization: (1) `echo "$INSTALL_DIR" >> "$GITHUB_PATH"` — INSTALL_DIR is derived from `${RUNNER_TEMP:-/tmp}/pipelock-bin` where RUNNER_TEMP is an inherited process env var from the calling workflow (treated as untrusted in composite actions); (2) `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"` — VERSION is derived from PIPELOCK_VERSION which is `${{ inputs.version }}` (user-controlled). A newline in either value could inject additional entries into GITHUB_PATH or GITHUB_OUTPUT.

Locations:

- `action.yml:160`

### github-env-injection (severity: high)

In the 'Run audit' step, `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"` writes an unsanitized user-controlled value to GITHUB_OUTPUT without applying `printf '%s' ... | tr -d '\n\r'` sanitization. CONFIG is derived from PIPELOCK_CONFIG which is set to `${{ inputs.config }}` (user-controlled input). A newline embedded in the config path input could inject additional key=value pairs into GITHUB_OUTPUT.

Locations:

- `action.yml:231`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line ~160): Sanitized INSTALL_DIR before writing to GITHUB_PATH using `safe_install_dir=$(printf '%s' "$INSTALL_DIR" | tr -d '\n\r')`, and sanitized VERSION before writing to GITHUB_OUTPUT using `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')`.
2. 'Run audit' step (line ~231): Sanitized CONFIG before writing to GITHUB_OUTPUT using `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')`. The auto-generated config path (REPORT_DIR/suggested.yaml) is not user-controlled so it was left as-is.

