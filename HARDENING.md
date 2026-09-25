<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

In the 'Download Pipelock' step, the shell variable VERSION (derived from inputs.version via env var PIPELOCK_VERSION) is written to $GITHUB_OUTPUT without sanitization: `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. When inputs.version is not 'latest', the user-supplied value flows through only a prefix-strip (`${VERSION#v}`) before being written. A value containing embedded newlines could inject additional key=value pairs into GITHUB_OUTPUT. The required sanitization (`safe=$(printf '%s' "$VERSION" | tr -d '\n\r')`) is absent.

Locations:

- `action.yml:148`

### github-env-injection (severity: high)

In the 'Run audit' step, the shell variable CONFIG (set from $PIPELOCK_CONFIG which comes from inputs.config) is written to $GITHUB_OUTPUT without sanitization: `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"`. A caller-supplied inputs.config value containing embedded newlines could inject additional key=value pairs into GITHUB_OUTPUT. The required sanitization (`safe=$(printf '%s' "$CONFIG" | tr -d '\n\r')`) is absent.

Locations:

- `action.yml:222`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in hardened/action/action.yml:
1. 'Download Pipelock' step: sanitized VERSION before writing to GITHUB_OUTPUT using `safe_version=$(printf '%s' "$VERSION" | tr -d '\n\r')` and writing `safe_version` instead.
2. 'Run audit' step: sanitized CONFIG before writing to GITHUB_OUTPUT using `safe_config=$(printf '%s' "$CONFIG" | tr -d '\n\r')` and writing `safe_config` instead.
Both fixes strip embedded newlines (\n) and carriage returns (\r) from user-supplied input values before they are written to $GITHUB_OUTPUT, preventing injection of additional key=value pairs.

