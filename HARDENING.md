<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags instead of immutable 40-character SHA digests. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised. Failing references: `actions/setup-python@v5`, `docker/setup-buildx-action@v3`, `docker/login-action@v3`.

Locations:

- `action.yml:44`
- `action.yml:50`
- `action.yml:52`

### script-injection (severity: high)

Two `run:` blocks in action.yml directly interpolate GitHub Actions expressions (`${{ ... }}`) inside shell command strings (sub-rule a). Before the shell ever sees the command, YAML template substitution replaces these expressions with raw, unquoted values that can contain shell metacharacters, enabling command injection by a caller who controls the inputs.

**Step: 'Install Python dependencies'** (line ~47): `pip install -r ${{ github.action_path }}/requirements.txt` — `github.action_path` is interpolated directly into the shell command.

**Step: 'Run action'** (line ~55 onward): Multiple direct interpolations inside the shell script:
- `args=( "--token" "${{ inputs.token }}" )`
- `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`
- `if [[ -n "${{ inputs.repository }}" ]]; then`
- `args+=( "--repository" "${{ inputs.repository }}" )`
- `if [[ -n "${{ inputs.package_name }}" ]]; then`
- `args+=( "--package_names" "${{ inputs.package_name }}" )`
- `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`
- `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`
- `args+=( "--with_sigs" "${{ inputs.with_sigs }}")`
- `args+=( "--owner_type" "${{ inputs.owner_type }}" )`
- `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"`

All `inputs.*` values should be passed via `env:` variables and then referenced as double-quoted shell variables (e.g. `"$INPUT_TOKEN"`) inside the `run:` block.

Locations:

- `action.yml:47`
- `action.yml:55`

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

Fixed all findings in action.yml:
1. Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065
2. Pinned docker/setup-buildx-action@v3 to SHA 8d2750c68a42422c14e847fe6c8ac0403b4cbd6f
3. Pinned docker/login-action@v3 to SHA c94ce9fb468520275223c153574b00df6fe4bcc9
4. Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions from both run: blocks into env: blocks, referencing them as plain shell variables ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, $INPUT_REPOSITORY, $INPUT_PACKAGE_NAME, $INPUT_UNTAGGED_ONLY, $INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, $INPUT_WITH_SIGS, $INPUT_OWNER_TYPE, $ACTION_PATH) to prevent script injection.

