<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references three external actions using mutable version tags instead of full 40-character SHA commit hashes. An attacker who compromises the upstream action repository could push malicious code under the same tag. Failing references:
- `uses: actions/setup-python@v5` (line 43)
- `uses: docker/setup-buildx-action@v3` (line 48)
- `uses: docker/login-action@v3` (line 50)
Each should be pinned to a full SHA, e.g. `actions/setup-python@<40-char-sha> # v5`.

Locations:

- `action.yml:43`
- `action.yml:48`
- `action.yml:50`

### script-injection (severity: high)

Rule (a): Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings, bypassing shell quoting and allowing an attacker-controlled value to inject arbitrary shell commands.

In the 'Install Python dependencies' step (line 46), `${{ github.action_path }}` is interpolated directly in a `run:` command:
  `pip install -r ${{ github.action_path }}/requirements.txt`

In the 'Run action' step (lines 54–67), the following expressions are all interpolated directly inside shell commands:
  - `"${{ inputs.token }}"`
  - `"${{ inputs.repository_owner }}"`
  - `"${{ inputs.repository }}"`
  - `"${{ inputs.package_name }}"`
  - `"${{ inputs.untagged_only }}"`
  - `"${{ inputs.except_untagged_multiplatform }}"`
  - `"${{ inputs.with_sigs }}"`
  - `"${{ inputs.owner_type }}"`
  - `${{ github.action_path }}/clean_ghcr.py`

All of these should be moved to `env:` variables and referenced as quoted shell variables (e.g. `"$INPUT_TOKEN"`) to prevent shell injection.

Locations:

- `action.yml:46`
- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.token }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:66`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository_owner }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:67`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:68`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:69`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package_name }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:71`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package_name }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:72`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.untagged_only }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:74`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.except_untagged_multiplatform }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:75`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.with_sigs }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:76`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.owner_type }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:77`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:

1. **unpinned-uses**: Pinned all three external actions to full 40-character SHA commit hashes:
   - `actions/setup-python@v5` → `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`
   - `docker/setup-buildx-action@v3` → `docker/setup-buildx-action@8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3`
   - `docker/login-action@v3` → `docker/login-action@c94ce9fb468520275223c153574b00df6fe4bcc9 # v3`

2. **script-injection / static-inline-injection**: Moved all `${{ }}` expressions from `run:` blocks to `env:` variables:
   - In 'Install Python dependencies' step: moved `${{ github.action_path }}` to `ACTION_PATH` env var
   - In 'Run action' step: moved all 8 input expressions (`inputs.token`, `inputs.repository_owner`, `inputs.repository`, `inputs.package_name`, `inputs.untagged_only`, `inputs.except_untagged_multiplatform`, `inputs.with_sigs`, `inputs.owner_type`) and `github.action_path` to env vars, then referenced them as quoted shell variables (`"$INPUT_TOKEN"`, `"$INPUT_REPOSITORY_OWNER"`, etc.)

