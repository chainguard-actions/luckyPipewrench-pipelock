<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the variable VERSION is derived from inputs.version (via the env var PIPELOCK_VERSION) and written directly to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. A caller supplying a version string containing newlines could inject arbitrary key=value pairs into $GITHUB_OUTPUT. The required sanitization step (`printf '%s' "$VERSION" | tr -d '\n\r'`) is missing before the write.

Locations:

- `action.yml:148`

### github-env-injection (severity: high)

In the 'Run audit' step, the variable CONFIG is derived from inputs.config (via the env var PIPELOCK_CONFIG) and written directly to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. A caller supplying a config path containing newlines could inject arbitrary key=value pairs into $GITHUB_OUTPUT. The required sanitization step (`printf '%s' "$CONFIG" | tr -d '\n\r'`) is missing before the write.

Locations:

- `action.yml:220`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step: sanitized VERSION before writing to $GITHUB_OUTPUT by adding `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and using `safe_version` in the echo.
2. 'Run audit' step: sanitized CONFIG before writing to $GITHUB_OUTPUT by adding `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` and using `safe_config` in the echo.
Both fixes strip newline (\n) and carriage return (\r) characters from user-controlled inputs before writing to $GITHUB_OUTPUT, preventing newline injection attacks.

### Iteration 2

**Fixes applied:** script-injection, broad-permissions

**Notes:**

1. ci.yaml: Fixed script-injection in the 'Required check compatibility gate' step of the 'test' job. Moved ${{ needs.test-oss.result }} and ${{ needs.test-enterprise.result }} into an env: block as TEST_OSS_RESULT and TEST_ENTERPRISE_RESULT respectively, and updated the run: script to reference them as plain shell variables. 2. scorecard.yaml: Replaced the broad top-level 'permissions: read-all' with specific minimal 'permissions: contents: read'. The job-level permissions (security-events: write, id-token: write) already provide the specific write access needed by the Scorecard action and CodeQL upload steps.

