<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.0** was hardened automatically. 14 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ inputs.* }} and ${{ github.* }} expressions are directly interpolated inside run: shell commands in action.yml. In the 'Install Python dependencies' step, ${{ github.action_path }} is interpolated directly in a shell command. In the 'Run action' step, ${{ inputs.token }}, ${{ inputs.repository_owner }}, ${{ inputs.repository }}, ${{ inputs.package_name }}, ${{ inputs.untagged_only }}, ${{ inputs.except_untagged_multiplatform }}, ${{ inputs.with_sigs }}, ${{ inputs.owner_type }}, and ${{ github.action_path }} are all interpolated directly into shell commands. An attacker controlling any of these inputs can inject arbitrary shell commands.

Locations:

- `action.yml:43`
- `action.yml:47`

### script-injection (severity: high)

Sub-rule (a): In .github/workflows/linter.yml, ${{ github.sha }} is directly interpolated in a run: shell command in the 'Cut sha for PR branch name' step (e.g. `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV`). Additionally, ${{ env.GITHUB_SHA_SHORT }} and ${{ env.IS_FORMATTED }} are interpolated directly in run: blocks. Any ${{ ... }} expression inside a run: block is a script-injection risk as YAML template substitution occurs before the shell ever sees the value.

Locations:

- `.github/workflows/linter.yml:22`
- `.github/workflows/linter.yml:23`
- `.github/workflows/linter.yml:33`

### github-env-injection (severity: high)

In .github/workflows/linter.yml, the 'Cut sha for PR branch name' step writes ${{ github.sha }} (an untrusted GitHub context value) directly to $GITHUB_ENV without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). The offending line is: `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV`. A value containing newlines could inject additional environment variables.

Locations:

- `.github/workflows/linter.yml:22`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable tags instead of full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the tag is moved. Unpinned references found:
- action.yml: actions/setup-python@v5, docker/setup-buildx-action@v3, docker/login-action@v3
- .github/workflows/linter.yml: actions/checkout@v4, actions/setup-python@v5
- .github/workflows/reusable.yml: actions/checkout@v4, docker/setup-buildx-action@v3, docker/login-action@v3, docker/build-push-action@v5
- .github/workflows/signed.yml: actions/checkout@v4
- .github/workflows/test.yml: actions/checkout@v4, docker/setup-buildx-action@v3, docker/login-action@v3
Note: sigstore/cosign-installer in reusable.yml IS correctly pinned to a SHA.

Locations:

- `action.yml:40`
- `action.yml:44`
- `action.yml:46`
- `.github/workflows/linter.yml:19`
- `.github/workflows/linter.yml:27`
- `.github/workflows/reusable.yml:29`
- `.github/workflows/reusable.yml:34`
- `.github/workflows/reusable.yml:36`
- `.github/workflows/reusable.yml:55`
- `.github/workflows/signed.yml:19`
- `.github/workflows/test.yml:19`
- `.github/workflows/test.yml:57`
- `.github/workflows/test.yml:60`

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

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, static-inline-injection

**Notes:**

Fixed all findings across action.yml and .github/workflows/linter.yml, reusable.yml, signed.yml, test.yml:

1. script-injection/static-inline-injection (action.yml): Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions from run: blocks into env: blocks. The 'Install Python dependencies' step uses ACTION_PATH env var. The 'Run action' step uses INPUT_TOKEN, INPUT_REPOSITORY_OWNER, INPUT_REPOSITORY, INPUT_PACKAGE_NAME, INPUT_UNTAGGED_ONLY, INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, INPUT_WITH_SIGS, INPUT_OWNER_TYPE, and ACTION_PATH env vars.

2. script-injection/github-env-injection (linter.yml): Moved ${{ github.sha }} into GITHUB_SHA_VALUE env var, sanitized with printf+tr before writing to GITHUB_ENV. Replaced ${{ env.IS_FORMATTED }} echo with local shell variable $is_formatted.

3. unpinned-uses: Pinned all mutable tag references to full commit SHAs:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (in linter.yml, reusable.yml, signed.yml x3, test.yml x8)
   - actions/setup-python@v5 → @a26af69be951a213d495a4c3e4e4022e16d87065 (in action.yml, linter.yml)
   - docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f (in action.yml, reusable.yml, test.yml)
   - docker/login-action@v3 → @c94ce9fb468520275223c153574b00df6fe4bcc9 (in action.yml, reusable.yml, test.yml)
   - docker/build-push-action@v5 → @ca052bb54ab0790a636c9b5f226502c73d547a25 (in reusable.yml)

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed all script-injection findings across three workflow files:
1. signed.yml: Moved `${{ steps.deleted-action.outputs.num_deleted }}` out of two run: blocks into env: blocks (NUM_DELETED), referencing as "$NUM_DELETED" in shell.
2. test.yml: Moved `${{ steps.deleted-action.outputs.num_deleted }}` out of seven run: blocks into env: blocks (NUM_DELETED), referencing as "$NUM_DELETED" in shell.
3. reusable.yml: Quoted `${DIGEST}` as `"${DIGEST}"` in the xargs cosign sign command to prevent unquoted shell expansion of the digest value.

