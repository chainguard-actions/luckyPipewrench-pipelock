<!-- markdownlint-disable -->

# Hardening Report: luckyPipewrench--pipelock/v3.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **luckyPipewrench--pipelock/v3.7.1** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### github-env-injection (severity: high)

The 'Download Pipelock' step sets VERSION from PIPELOCK_VERSION, which comes from `${{ inputs.version }}`. Apart from stripping a leading 'v', the value is not cleaned before `echo "version=${VERSION}" >> "$GITHUB_OUTPUT"`. The same step also writes `echo "$INSTALL_DIR" >> "$GITHUB_PATH"`. INSTALL_DIR is built from the inherited RUNNER_TEMP env var, which the script reads rather than sets. Neither write is preceded by `printf '%s' ... | tr -d '\n\r'`, so a value containing a newline could inject extra output or path entries.

Locations:

- `action.yml:176`
- `action.yml:177`

### github-env-injection (severity: high)

The 'Run audit' step sets CONFIG from PIPELOCK_CONFIG, which comes from `${{ inputs.config }}`. It writes the value with `echo "config_path=${CONFIG}" >> "$GITHUB_OUTPUT"` without cleaning it first. A config input containing a newline could add arbitrary step outputs, for example overwriting critical_count or audit_failed, which the 'Check results' gate reads. This could get around the gate. The same step also writes `config_path=${REPORT_DIR}/suggested.yaml`, where REPORT_DIR comes from the inherited RUNNER_TEMP, also without the `printf '%s' | tr -d '\n\r'` step.

Locations:

- `action.yml:281`
- `action.yml:289`

## Iteration Notes

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

In action.yml, newlines and carriage returns are now stripped with printf '%s' | tr -d '\n\r' before these values are written: INSTALL_DIR to GITHUB_PATH and VERSION to GITHUB_OUTPUT (Download Pipelock step), plus CONFIG and REPORT_DIR to the config_path output (Run audit step). I didn't touch the rest of the logic, and nothing was run or tested.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

In action.yml's 'Run audit' step, the earlier hardening attempt had left the script broken: the SAFE_REPORT_DIR line was merged into a comment, and part of the generate command was cut off. I restored the comment and the command (`pipelock audit "$DIR" -o "$REPORT_DIR/suggested.yaml" "${EXCLUDE_ARGS[@]}" >/dev/null 2>&1 || true`). I rebuilt the command from the leftover piece (`IR" -o ...`) and the README usage (`pipelock audit <dir> -o pipelock.yaml`), because no original copy of the line survives in the repo. The second line of the comment is also my own wording, since the original text was lost. Inside the `if [ -f ... ]` branch, SAFE_REPORT_DIR is now computed with printf '%s' | tr -d '\n\r' on its own line, and the config_path write to $GITHUB_OUTPUT uses it. Nothing was run to test this.

