<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if the referenced tags are moved or overwritten:
- `uses: actions/setup-python@v5` (line 44)
- `uses: docker/setup-buildx-action@v3` (line 49)
- `uses: docker/login-action@v3` (line 51)

Locations:

- `action.yml:44`
- `action.yml:49`
- `action.yml:51`

### script-injection (severity: high)

Multiple `run:` blocks in action.yml directly interpolate `${{ ... }}` expressions inside shell command strings (sub-rule a), allowing an attacker-controlled value to be parsed by the shell before quoting takes effect.

1. Line 48 — `pip install -r ${{ github.action_path }}/requirements.txt`: `github.action_path` is interpolated directly into the shell command.

2. Lines 60–72 — The 'Run action' step interpolates all inputs directly into the shell:
   - `"--token" "${{ inputs.token }}"`
   - `"--repository_owner" "${{ inputs.repository_owner }}"`
   - `[[ -n "${{ inputs.repository }}" ]]` and `"${{ inputs.repository }}"`
   - `[[ -n "${{ inputs.package_name }}" ]]` and `"${{ inputs.package_name }}"`
   - `"${{ inputs.untagged_only }}"`
   - `"${{ inputs.except_untagged_multiplatform }}"`
   - `"${{ inputs.with_sigs }}"`
   - `"${{ inputs.owner_type }}"`
   - `python ${{ github.action_path }}/clean_ghcr.py`

All `${{ inputs.* }}` values are caller-controlled and must be passed via `env:` variables and then referenced as quoted shell variables, never interpolated directly into `run:` scripts.

Locations:

- `action.yml:48`
- `action.yml:60`
- `action.yml:61`
- `action.yml:62`
- `action.yml:64`
- `action.yml:65`
- `action.yml:67`
- `action.yml:68`
- `action.yml:69`
- `action.yml:70`
- `action.yml:72`

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
4. Moved github.action_path out of both run: blocks into env: as ACTION_PATH
5. Moved all inputs.* expressions (token, repository_owner, repository, package_name, untagged_only, except_untagged_multiplatform, with_sigs, owner_type) out of the 'Run action' run: block into an env: map, referencing them as plain shell variables ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, etc.)

