<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.0.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings in action.yml, violating sub-rule (a). This allows a caller to inject arbitrary shell commands via crafted input values.

**Step: "Install Python dependencies" (line 47):**
`run: pip install -r ${{ github.action_path }}/requirements.txt`

**Step: "Run action" (lines 56–69):**
- `args=( "--token" "${{ inputs.token }}" )` — line 56
- `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )` — line 57
- `if [[ -n "${{ inputs.repository }}" ]]; then` — line 58
- `args+=( "--repository" "${{ inputs.repository }}" )` — line 59
- `if [[ -n "${{ inputs.package_name }}" ]]; then` — line 61
- `args+=( "--package_names" "${{ inputs.package_name }}" )` — line 62
- `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )` — line 64
- `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )` — line 65
- `args+=( "--with_sigs" "${{ inputs.with_sigs }}")` — line 66
- `args+=( "--owner_type" "${{ inputs.owner_type }}" )` — line 67
- `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"` — line 69

All `inputs.*` values should be passed via `env:` variables and referenced as `"$ENV_VAR"` in the shell script instead.

Locations:

- `action.yml:47`
- `action.yml:56`
- `action.yml:57`
- `action.yml:58`
- `action.yml:59`
- `action.yml:61`
- `action.yml:62`
- `action.yml:64`
- `action.yml:65`
- `action.yml:66`
- `action.yml:67`
- `action.yml:69`

### unpinned-uses (severity: high)

All three `uses:` references in action.yml use mutable version tags instead of pinned 40-character commit SHA digests. This exposes the action to supply-chain attacks where a compromised upstream action tag could silently execute malicious code.

Failing references:
- `uses: actions/setup-python@v5` (line 43)
- `uses: docker/setup-buildx-action@v3` (line 48)
- `uses: docker/login-action@v3` (line 50)

Each should be pinned to a full SHA, e.g. `uses: actions/setup-python@<40-char-sha> # v5`.

Locations:

- `action.yml:43`
- `action.yml:48`
- `action.yml:50`

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

Fixed all findings in action.yml: (1) Pinned all three uses: references to full 40-char SHAs: actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5, docker/setup-buildx-action@8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3, docker/login-action@c94ce9fb468520275223c153574b00df6fe4bcc9 # v3. (2) Moved all ${{ }} expressions out of run: blocks into env: blocks for both the 'Install Python dependencies' step (github.action_path -> ACTION_PATH) and the 'Run action' step (all 9 inputs plus github.action_path mapped to INPUT_TOKEN, INPUT_REPOSITORY_OWNER, INPUT_REPOSITORY, INPUT_PACKAGE_NAME, INPUT_UNTAGGED_ONLY, INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, INPUT_WITH_SIGS, INPUT_OWNER_TYPE, ACTION_PATH). Shell script now references plain env vars instead of ${{ }} expressions.

