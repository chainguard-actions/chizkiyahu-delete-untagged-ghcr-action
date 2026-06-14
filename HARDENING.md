<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.1** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned full-length SHA commit hashes, making the action vulnerable to supply-chain attacks if those tags are moved:
- `uses: actions/setup-python@v6`
- `uses: docker/setup-buildx-action@v4`
- `uses: docker/login-action@v4`
Each should be pinned to a full 40-character hex commit SHA (e.g. `actions/setup-python@<sha> # v6`).

Locations:

- `action.yml:44`
- `action.yml:49`
- `action.yml:51`

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings (rule a), allowing an attacker who controls those inputs to inject arbitrary shell commands before the shell ever sees them.

In the 'Install Python dependencies' step:
- `run: pip install -r ${{ github.action_path }}/requirements.txt` — `${{ github.action_path }}` is interpolated directly into the shell command.

In the 'Run action' step, all of the following are interpolated directly into shell array assignments and conditionals:
- `"--token" "${{ inputs.token }}"`
- `"--repository_owner" "${{ inputs.repository_owner }}"`
- `[[ -n "${{ inputs.repository }}" ]]` and `"--repository" "${{ inputs.repository }}"`
- `[[ -n "${{ inputs.package_name }}" ]]` and `"--package_names" "${{ inputs.package_name }}"`
- `"--untagged_only" "${{ inputs.untagged_only }}"`
- `"--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}"`
- `"--with_sigs" "${{ inputs.with_sigs }}"`
- `"--owner_type" "${{ inputs.owner_type }}"`
- `python ${{ github.action_path }}/clean_ghcr.py`

All `${{ inputs.* }}` and `${{ github.* }}` values should be passed via `env:` variables and then referenced as quoted shell variables (e.g. `"$INPUT_TOKEN"`) inside the `run:` block.

Locations:

- `action.yml:46`
- `action.yml:57`

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
1. unpinned-uses: Pinned actions/setup-python@v6 → @a309ff8b426b58ec0e2a45f0f869d46889d02405 # v6, docker/setup-buildx-action@v4 → @d7f5e7f509e45cec5c76c4d5afdd7de93d0b3df5 # v4, docker/login-action@v4 → @650006c6eb7dba73a995cc03b0b2d7f5ca915bee # v4.
2. script-injection / static-inline-injection: In the 'Install Python dependencies' step, moved ${{ github.action_path }} to env: as ACTION_PATH and referenced as "$ACTION_PATH/requirements.txt". In the 'Run action' step, moved all ${{ inputs.* }} expressions (token, repository_owner, repository, package_name, untagged_only, except_untagged_multiplatform, with_sigs, owner_type) and ${{ github.action_path }} to an env: block, then referenced them as plain shell variables ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, etc., $ACTION_PATH) throughout the run: block.

