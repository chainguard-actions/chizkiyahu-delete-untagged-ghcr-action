<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **chizkiyahu--delete-untagged-ghcr-action/v5.0.0** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v5`, which is a mutable tag reference rather than a pinned 40-character commit SHA. This means the referenced action could be silently replaced with a different (potentially malicious) version without any change to this file, creating a supply-chain risk.

Locations:

- `action.yml:43`

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings (sub-rule a), allowing an attacker-controlled value to break out of the intended string context and inject arbitrary shell commands.

Step 'Install Python dependencies' (line 46):
  `run: pip install -r ${{ github.action_path }}/requirements.txt`

Step 'Run action' (lines 50–62) — all of the following are direct interpolations:
  - `args=( "--token" "${{ inputs.token }}" )`
  - `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`
  - `if [[ -n "${{ inputs.repository }}" ]]; then`
  - `args+=( "--repository" "${{ inputs.repository }}" )`
  - `if [[ -n "${{ inputs.package_name }}" ]]; then`
  - `args+=( "--package_names" "${{ inputs.package_name }}" )`
  - `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`
  - `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`
  - `args+=( "--owner_type" "${{ inputs.owner_type }}" )`
  - `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"`

Fix: Move all `inputs.*` values into `env:` variables and reference them as quoted shell variables (e.g., `"$INPUT_TOKEN"`). Use `$GITHUB_ACTION_PATH` (the pre-set env var) instead of `${{ github.action_path }}`.

Locations:

- `action.yml:46`
- `action.yml:50`
- `action.yml:51`
- `action.yml:52`
- `action.yml:53`
- `action.yml:55`
- `action.yml:56`
- `action.yml:58`
- `action.yml:59`
- `action.yml:60`
- `action.yml:62`

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

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned `actions/setup-python@v5` to full SHA `a26af69be951a213d495a4c3e4e4022e16d87065` with `# v5` comment. 2. Replaced `${{ github.action_path }}` with `$GITHUB_ACTION_PATH` in the 'Install Python dependencies' step. 3. In the 'Run action' step, moved all `${{ inputs.* }}` expressions into an `env:` block (INPUT_TOKEN, INPUT_REPOSITORY_OWNER, INPUT_REPOSITORY, INPUT_PACKAGE_NAME, INPUT_UNTAGGED_ONLY, INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, INPUT_OWNER_TYPE) and updated the shell script to reference them as quoted `"$VAR_NAME"` variables. Also replaced `${{ github.action_path }}` with `$GITHUB_ACTION_PATH` in the python invocation.

