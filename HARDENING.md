<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.0.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised:
- `actions/setup-python@v5` (line 46)
- `docker/setup-buildx-action@v3` (line 52)
- `docker/login-action@v3` (line 54)
Each should be replaced with a full SHA pin, e.g. `actions/setup-python@<40-hex-sha> # v5`.

Locations:

- `action.yml:46`
- `action.yml:52`
- `action.yml:54`

### script-injection (severity: high)

Rule (a): GitHub Actions expressions (`${{ ... }}`) are interpolated directly inside `run:` shell command strings, allowing an attacker-controlled value to inject arbitrary shell commands before the shell ever parses the string.

**Step: 'Install Python dependencies' (line 50):** `run: pip install -r ${{ github.action_path }}/requirements.txt` — `github.action_path` is interpolated directly into the shell command.

**Step: 'Run action' (lines 60–71):** Multiple `inputs.*` and `github.action_path` expressions are interpolated directly into shell array assignments and a `python` invocation:
- `"--token" "${{ inputs.token }}"`
- `"--repository_owner" "${{ inputs.repository_owner }}"`
- `"${{ inputs.repository }}"`
- `"${{ inputs.package_name }}"`
- `"${{ inputs.untagged_only }}"`
- `"${{ inputs.except_untagged_multiplatform }}"`
- `"${{ inputs.with_sigs }}"`
- `"${{ inputs.owner_type }}"`
- `python ${{ github.action_path }}/clean_ghcr.py`

Fix: Move all `${{ inputs.* }}` values into `env:` variables and reference them as quoted shell variables (e.g. `"$INPUT_TOKEN"`), and use the `$GITHUB_ACTION_PATH` environment variable instead of `${{ github.action_path }}`.

Locations:

- `action.yml:50`
- `action.yml:60`

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

Fixed all three unpinned action references by pinning to full commit SHAs (actions/setup-python@a26af69..., docker/setup-buildx-action@8d2750c..., docker/login-action@c94ce9f...). Fixed all script injection findings by moving every ${{ inputs.* }} and ${{ github.action_path }} expression out of run: shell strings into env: blocks, then referencing them as plain shell variables ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, $INPUT_REPOSITORY, $INPUT_PACKAGE_NAME, $INPUT_UNTAGGED_ONLY, $INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, $INPUT_WITH_SIGS, $INPUT_OWNER_TYPE, $ACTION_PATH) in the shell scripts.

