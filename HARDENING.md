<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.0.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags instead of full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if those tags are moved:
- `actions/setup-python@v5` (line 47)
- `docker/setup-buildx-action@v3` (line 52)
- `docker/login-action@v3` (line 54)
These should be replaced with their corresponding full commit SHAs.

Locations:

- `action.yml:47`
- `action.yml:52`
- `action.yml:54`

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings (sub-rule a), allowing an attacker-controlled input to inject arbitrary shell commands via metacharacters before the shell ever sees the value.

Affected lines in the 'Run action' step:
- Line 62: `args=( "--token" "${{ inputs.token }}" )`
- Line 63: `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`
- Line 64: `if [[ -n "${{ inputs.repository }}" ]]; then`
- Line 65: `args+=( "--repository" "${{ inputs.repository }}" )`
- Line 68: `args+=( "--package_names" "${{ inputs.package_name }}" )`
- Line 70: `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`
- Line 71: `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`
- Line 72: `args+=( "--with_sigs" "${{ inputs.with_sigs }}")`
- Line 73: `args+=( "--owner_type" "${{ inputs.owner_type }}" )`
- Line 75: `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"`

Also affected in the 'Install Python dependencies' step:
- Line 50: `run: pip install -r ${{ github.action_path }}/requirements.txt`

Fix: Move all `inputs.*` values into `env:` variables and reference them as quoted shell variables (e.g., `"$INPUT_TOKEN"`) inside the `run:` block. Use `$GITHUB_ACTION_PATH` (the pre-set env var) instead of `${{ github.action_path }}`.

Locations:

- `action.yml:50`
- `action.yml:62`
- `action.yml:63`
- `action.yml:64`
- `action.yml:65`
- `action.yml:68`
- `action.yml:70`
- `action.yml:71`
- `action.yml:72`
- `action.yml:73`
- `action.yml:75`

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

Fixed all three findings in action.yml:
1. Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065, docker/setup-buildx-action@v3 to SHA 8d2750c68a42422c14e847fe6c8ac0403b4cbd6f, and docker/login-action@v3 to SHA c94ce9fb468520275223c153574b00df6fe4bcc9.
2. Moved all ${{ inputs.* }} expressions (token, repository_owner, repository, package_name, untagged_only, except_untagged_multiplatform, with_sigs, owner_type) into an env: block on the 'Run action' step, referencing them as $INPUT_* shell variables.
3. Replaced ${{ github.action_path }} with the pre-set $GITHUB_ACTION_PATH environment variable in both the 'Install Python dependencies' and 'Run action' steps.

