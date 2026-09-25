<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v5.0.0** was hardened automatically. 11 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v5`, which is pinned to a mutable version tag (`@v5`) rather than an immutable 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to this file, creating a supply-chain risk.

Locations:

- `action.yml:42`

### script-injection (severity: high)

Sub-rule (a): Two `run:` blocks in action.yml directly interpolate `${{ ... }}` GitHub Actions expressions inside shell command strings, enabling script injection.

1. "Install Python dependencies" step (line 45): `pip install -r ${{ github.action_path }}/requirements.txt` — `github.action_path` is interpolated directly into the shell command before the shell parses it.

2. "Run action" step (lines 50–62): Multiple `inputs.*` values are interpolated directly into shell commands:
   - `"${{ inputs.token }}"`
   - `"${{ inputs.repository_owner }}"`
   - `"${{ inputs.repository }}"`
   - `"${{ inputs.package_name }}"`
   - `"${{ inputs.untagged_only }}"`
   - `"${{ inputs.except_untagged_multiplatform }}"`
   - `"${{ inputs.owner_type }}"`
   - `python ${{ github.action_path }}/clean_ghcr.py`

An attacker who controls any of these inputs can inject shell metacharacters (e.g. `"; malicious_command; "`) that are executed by the shell before quoting takes effect. The fix is to pass all inputs via `env:` variables and reference them as properly double-quoted shell variables (e.g., `"$INPUT_TOKEN"`).

Locations:

- `action.yml:45`
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

Fixed all findings in action.yml:
1. Pinned actions/setup-python@v5 to full commit SHA a26af69be951a213d495a4c3e4e4022e16d87065 (keeping # v5 comment).
2. Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions out of run: blocks into env: blocks for both the 'Install Python dependencies' step and the 'Run action' step. Shell scripts now reference plain environment variables ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, $INPUT_REPOSITORY, $INPUT_PACKAGE_NAME, $INPUT_UNTAGGED_ONLY, $INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, $INPUT_OWNER_TYPE, $ACTION_PATH), eliminating all script injection vectors.

