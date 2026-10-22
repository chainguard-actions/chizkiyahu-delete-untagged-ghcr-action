<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v5.0.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): a GitHub Actions expression is placed directly inside a run: block. Offending line: `run: pip install -r ${{ github.action_path }}/requirements.txt`. Pass github.action_path through env and quote it, e.g. "$GITHUB_ACTION_PATH/requirements.txt".

Locations:

- `action.yml:49`

### script-injection (severity: high)

Sub-rule (a): the 'Run action' step places many expressions directly inside its run: script, so a crafted input can break out of the quotes and run commands. Offending lines include: `args=( "--token" "${{ inputs.token }}" )`, `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`, `if [[ -n "${{ inputs.repository }}" ]]`, `args+=( "--package_names" "${{ inputs.package_name }}" )`, `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`, `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`, `args+=( "--owner_type" "${{ inputs.owner_type }}" )`, `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"`. Move each value into an env: variable and use it double-quoted.

Locations:

- `action.yml:53`

### unpinned-uses (severity: high)

The composite step points to a version tag instead of a full commit SHA: `uses: actions/setup-python@v5`.

Locations:

- `action.yml:44`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.token }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository_owner }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:55`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package_name }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:59`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package_name }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.untagged_only }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:62`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.except_untagged_multiplatform }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:63`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.owner_type }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:64`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, static-inline-injection

**Notes:**

Pinned actions/setup-python to a26af69be951a213d495a4c3e4e4022e16d87065 (v5). Replaced ${{ github.action_path }} with "$GITHUB_ACTION_PATH" in quotes. Moved all inputs used in the 'Run action' step into env: vars (INPUT_*) and pass them double-quoted into the bash args array. The commands still get the same arguments: optional inputs are only added when they are non-empty, as before. clean_ghcr.py reads only GITHUB_OUTPUT from the environment, so the new INPUT_* env vars don't clash with anything.

