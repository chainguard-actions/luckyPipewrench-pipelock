<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.4.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the variable VERSION (derived from inputs.version via env var PIPELOCK_VERSION) is written to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. An attacker-controlled input containing newline characters could inject additional key=value pairs into the GitHub output file, potentially overwriting other outputs or injecting environment variables in downstream steps. The required sanitization step (`printf '%s' "$VERSION" | tr -d '\n\r'`) is absent.

Locations:

- `action.yml:163`

### github-env-injection (severity: high)

In the 'Run audit' step, the variable CONFIG (derived from inputs.config via env var PIPELOCK_CONFIG) is written to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker-controlled input containing newline characters could inject additional key=value pairs into the GitHub output file. The required sanitization step (`printf '%s' "$CONFIG" | tr -d '\n\r'`) is absent before the write.

Locations:

- `action.yml:222`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in action.yml:
1. 'Download Pipelock' step (line 163): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT, replacing the unsanitized `echo "version=${VERSION}"` with `echo "version=${safe_version}"`.
2. 'Run audit' step (line 222): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing to $GITHUB_OUTPUT, replacing the unsanitized `echo "config_path=${CONFIG}"` with `echo "config_path=${safe_config}"`.
Both fixes strip newline and carriage return characters from attacker-controlled input values before they are written to the GitHub output file, preventing newline injection attacks.

### Iteration 2

**Fixes applied:** script-injection, broad-permissions

**Notes:**

1. Fixed script-injection in .github/workflows/ci.yaml: Moved ${{ needs.test-oss.result }}, ${{ needs.test-enterprise.result }}, and ${{ needs.test-subprocess-coverage.result }} expressions from the run: block into an env: block as TEST_OSS_RESULT, TEST_ENTERPRISE_RESULT, and TEST_SUBPROCESS_COVERAGE_RESULT respectively. The shell commands now reference these as plain environment variables. 2. Fixed broad-permissions in .github/workflows/scorecard.yaml: Replaced top-level 'permissions: read-all' with 'permissions: contents: read' (the minimal permission needed for the checkout step). The job-level id-token: write permission was already correctly scoped at the job level.

