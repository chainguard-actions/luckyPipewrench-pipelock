<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.5.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the variable VERSION is derived from inputs.version (via env var PIPELOCK_VERSION, which is user-controlled) and written to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. An attacker-supplied newline in inputs.version could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting other outputs or injecting environment variables consumed by downstream steps.

Locations:

- `action.yml:148`

### github-env-injection (severity: high)

In the 'Run audit' step, the variable CONFIG is derived from inputs.config (via env var PIPELOCK_CONFIG, which is user-controlled) and written to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. An attacker-supplied newline in inputs.config could inject arbitrary key=value pairs into GITHUB_OUTPUT, potentially overwriting other outputs or injecting environment variables consumed by downstream steps.

Locations:

- `action.yml:225`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step (line ~148): Added `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` before writing to GITHUB_OUTPUT, replacing the direct `echo "version=${VERSION}"` with `echo "version=${safe_version}"`.
2. 'Run audit' step (line ~225): Added `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` before writing the user-provided config path to GITHUB_OUTPUT. Also sanitized the auto-generated suggested.yaml path for consistency using `safe_suggested=$(printf '%s' "${REPORT_DIR}/suggested.yaml" | tr -d '\n\r')`.

### Iteration 2

**Fixes applied:** script-injection, broad-permissions

**Notes:**

Fixed script injection in ci.yaml test-go125 and test-go126 jobs by moving all ${{ needs.*.result }} expressions out of run: blocks and into env: blocks, then referencing them as plain shell variables ($SECURITY_SCAN_RESULT, etc.). Fixed broad-permissions in scorecard.yaml by replacing top-level `permissions: read-all` with `permissions: contents: read`; the analysis job already had `id-token: write` at the job level for OIDC publishing.

