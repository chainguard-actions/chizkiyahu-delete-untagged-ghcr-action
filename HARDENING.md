<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.1** was hardened automatically. 13 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml has composite steps that point to version tags, not full 40-character commit SHAs: `uses: actions/setup-python@v6`, `uses: docker/setup-buildx-action@v4`, `uses: docker/login-action@v4`.

Locations:

- `action.yml:48`
- `action.yml:55`
- `action.yml:57`

### script-injection (severity: high)

Rule (a): the 'Install Python dependencies' step puts an expression straight into the shell command: `run: pip install -r ${{ github.action_path }}/requirements.txt`. Fix: pass the path through env (or use $GITHUB_ACTION_PATH) and double-quote it.

Locations:

- `action.yml:53`

### script-injection (severity: high)

Rule (a): the 'Run action' step puts many expressions straight into the run script, for example `args=( "--token" "${{ inputs.token }}" )`, `"${{ inputs.repository_owner }}"`, `"${{ inputs.repository }}"`, `"${{ inputs.package_name }}"`, `"${{ inputs.untagged_only }}"`, `"${{ inputs.except_untagged_multiplatform }}"`, `"${{ inputs.with_sigs }}"`, `"${{ inputs.owner_type }}"` and `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"`. A caller who supplies an input value can inject shell commands. Fix: move each value into the step's env: block and refer to it as a double-quoted shell variable.

Locations:

- `action.yml:65`

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

Pinned actions/setup-python, docker/setup-buildx-action and docker/login-action to full commit SHAs, with the tag kept as a comment. The 'Install Python dependencies' and 'Run action' steps now use "$GITHUB_ACTION_PATH" instead of the expression. All inputs are passed through the env: block and used as double-quoted shell variables in the existing args array, so the arguments are the same as before.

