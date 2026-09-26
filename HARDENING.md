<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.1** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags instead of immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or overwritten. Failing references: `actions/setup-python@v6` (line 45), `docker/setup-buildx-action@v4` (line 49), `docker/login-action@v4` (line 51). Each should be replaced with the full commit SHA, e.g. `actions/setup-python@<40-hex-sha> # v6`.

Locations:

- `action.yml:45`
- `action.yml:49`
- `action.yml:51`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in action.yml directly interpolate GitHub Actions expressions (`${{ inputs.* }}` and `${{ github.* }}`) into shell command strings. This means YAML template substitution occurs before the shell ever sees the value, allowing an attacker-controlled input to inject arbitrary shell metacharacters. Affected steps and offending lines:

1. 'Install Python dependencies' (line 48): `pip install -r ${{ github.action_path }}/requirements.txt` — `${{ github.action_path }}` is interpolated directly into the shell command.

2. 'Run action' (lines 58–70): All eight inputs (`${{ inputs.token }}`, `${{ inputs.repository_owner }}`, `${{ inputs.repository }}`, `${{ inputs.package_name }}`, `${{ inputs.untagged_only }}`, `${{ inputs.except_untagged_multiplatform }}`, `${{ inputs.with_sigs }}`, `${{ inputs.owner_type }}`) and `${{ github.action_path }}` are interpolated directly into shell commands. Fix: move each value into an `env:` block and reference it as a quoted shell variable (e.g. `"$INPUT_TOKEN"`).

Locations:

- `action.yml:48`
- `action.yml:58`
- `action.yml:59`
- `action.yml:60`
- `action.yml:62`
- `action.yml:64`
- `action.yml:65`
- `action.yml:66`
- `action.yml:67`
- `action.yml:68`
- `action.yml:70`

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

1. **unpinned-uses**: Pinned all three `uses:` references to full 40-char SHAs:
   - `actions/setup-python@v6` → `@ece7cb06caefa5fff74198d8649806c4678c61a1 # v6`
   - `docker/setup-buildx-action@v4` → `@f87e5991a6d7451dcb8d9637bfbc97413f497069 # v4`
   - `docker/login-action@v4` → `@dbcb813823bdd20940b903addbd779551569679f # v4`

2. **script-injection / static-inline-injection**: Moved all `${{ inputs.* }}` and `${{ github.action_path }}` expressions out of `run:` shell strings and into `env:` blocks. In the 'Install Python dependencies' step, `github.action_path` is now `ACTION_PATH` env var. In the 'Run action' step, all 8 inputs and `github.action_path` are mapped to env vars (`INPUT_TOKEN`, `INPUT_REPOSITORY_OWNER`, `INPUT_REPOSITORY`, `INPUT_PACKAGE_NAME`, `INPUT_UNTAGGED_ONLY`, `INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM`, `INPUT_WITH_SIGS`, `INPUT_OWNER_TYPE`, `ACTION_PATH`) and referenced as quoted shell variables throughout the script.

