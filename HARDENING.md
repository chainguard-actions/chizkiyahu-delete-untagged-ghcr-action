<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.0.0** was hardened automatically. 17 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run action' step in action.yml directly interpolates multiple ${{ inputs.* }} and ${{ github.action_path }} expressions inside a run: shell script (rule a). This allows an attacker who controls the calling workflow to inject arbitrary shell commands. Offending lines include: `args=( "--token" "${{ inputs.token }}" )`, `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`, `args+=( "--repository" "${{ inputs.repository }}" )`, `args+=( "--package_names" "${{ inputs.package_name }}" )`, `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`, `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`, `args+=( "--with_sigs" "${{ inputs.with_sigs }}")`, `args+=( "--owner_type" "${{ inputs.owner_type }}" )`, and `python ${{ github.action_path }}/clean_ghcr.py`. All inputs should be passed via env: variables and referenced as quoted shell variables instead.

Locations:

- `action.yml:56`

### script-injection (severity: high)

The 'Cut sha for PR branch name' step in linter.yml directly interpolates ${{ github.sha }} inside a run: shell command (rule a): `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV`. Additionally, `echo ${{ env.GITHUB_SHA_SHORT }}` interpolates an env context expression directly in the shell. These expressions are substituted by the Actions runner before the shell sees them, enabling injection of shell metacharacters.

Locations:

- `.github/workflows/linter.yml:18`

### script-injection (severity: high)

Multiple run: blocks in signed.yml directly interpolate ${{ steps.deleted-action.outputs.num_deleted }} inside shell if-statements (rule a): `if [[ "${{ steps.deleted-action.outputs.num_deleted }}" != 2 ]]; then` and `if [[ "${{ steps.deleted-action.outputs.num_deleted }}" != 1 ]]; then`. Step outputs are workflow-controllable and must not be interpolated directly into shell scripts.

Locations:

- `.github/workflows/signed.yml:47`
- `.github/workflows/signed.yml:62`

### script-injection (severity: high)

Multiple run: blocks in test.yml directly interpolate ${{ steps.deleted-action.outputs.num_deleted }} inside shell if-statements (rule a), e.g.: `if [[ "${{ steps.deleted-action.outputs.num_deleted }}" != 18 ]]; then`. Step outputs are workflow-controllable and must not be interpolated directly into shell scripts. This pattern appears in the clean_untagged_pkgs1, clean_untagged_pkgs2, delete_package, delete_multiple_packages, and clean_repo jobs.

Locations:

- `.github/workflows/test.yml:72`
- `.github/workflows/test.yml:90`
- `.github/workflows/test.yml:107`
- `.github/workflows/test.yml:122`
- `.github/workflows/test.yml:137`

### github-env-injection (severity: high)

In linter.yml, the 'Cut sha for PR branch name' step writes a value derived from ${{ github.sha }} directly to $GITHUB_ENV without sanitization: `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV`. Although github.sha is not typically attacker-controlled, the expression is interpolated directly into the shell command before being written to GITHUB_ENV, and no `printf '%s' ... | tr -d '\n\r'` sanitization step is applied. The required sanitization pipeline must be applied before every write to a special environment file.

Locations:

- `.github/workflows/linter.yml:18`

### unpinned-uses (severity: high)

Multiple uses: references across action.yml and workflow files use mutable tag or version refs instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Unpinned references found:
- action.yml: `actions/setup-python@v5` (line 49), `docker/setup-buildx-action@v3` (line 51), `docker/login-action@v3` (line 53)
- linter.yml: `actions/checkout@v3` (line 14), `actions/setup-python@v5` (line 21), `peter-evans/create-pull-request@v4` (line 31)
- reusable.yml: `actions/checkout@v4` (line 32), `docker/setup-buildx-action@v3` (line 35), `docker/login-action@v3` (line 36), `docker/build-push-action@v5` (line 52)
- signed.yml: `actions/checkout@v4` (multiple steps)
- test.yml: `actions/checkout@v4` (multiple steps), `docker/setup-buildx-action@v3`, `docker/login-action@v3`

Locations:

- `action.yml:49`
- `action.yml:51`
- `action.yml:53`
- `.github/workflows/linter.yml:14`
- `.github/workflows/linter.yml:21`
- `.github/workflows/linter.yml:31`
- `.github/workflows/reusable.yml:32`
- `.github/workflows/reusable.yml:35`
- `.github/workflows/reusable.yml:36`
- `.github/workflows/reusable.yml:52`
- `.github/workflows/signed.yml:19`
- `.github/workflows/signed.yml:44`
- `.github/workflows/signed.yml:57`
- `.github/workflows/test.yml:18`
- `.github/workflows/test.yml:48`
- `.github/workflows/test.yml:51`
- `.github/workflows/test.yml:54`

### missing-permissions (severity: medium)

linter.yml has no top-level `permissions:` key and the single job 'formater' also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to all scopes). A minimal permissions block should be added.

Locations:

- `.github/workflows/linter.yml:1`

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

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, missing-permissions, static-inline-injection

**Notes:**

Fixed all findings across action.yml and workflow files:

1. action.yml: Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions out of the run: block into an env: map. Shell script now uses plain env vars ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, $INPUT_REPOSITORY, $INPUT_PACKAGE_NAME, $INPUT_UNTAGGED_ONLY, $INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, $INPUT_WITH_SIGS, $INPUT_OWNER_TYPE, $ACTION_PATH). Also pinned actions/setup-python@v5, docker/setup-buildx-action@v3, docker/login-action@v3 to full SHAs.

2. linter.yml: Added top-level permissions block (contents: read, pull-requests: write). Moved ${{ github.sha }} to env var GITHUB_SHA_INPUT with printf/tr sanitization before writing to GITHUB_ENV. Removed ${{ env.GITHUB_SHA_SHORT }} inline interpolation. Pinned actions/checkout@v3, actions/setup-python@v5, peter-evans/create-pull-request@v4 to full SHAs.

3. reusable.yml: Pinned actions/checkout@v4, docker/setup-buildx-action@v3, docker/login-action@v3, docker/build-push-action@v5 to full SHAs.

4. signed.yml: Moved ${{ steps.deleted-action.outputs.num_deleted }} to env: NUM_DELETED in both run: blocks. Pinned all actions/checkout@v4 references to full SHA.

5. test.yml: Moved ${{ steps.deleted-action.outputs.num_deleted }} to env: NUM_DELETED in all five run: blocks. Pinned actions/checkout@v4, docker/setup-buildx-action@v3, docker/login-action@v3 to full SHAs.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `${DIGEST}` variable in the 'Sign the published Docker image' step in `.github/workflows/reusable.yml`. Changed `{}@${DIGEST}` to `{}@"${DIGEST}"` to ensure the digest value is always properly quoted, preventing shell metacharacter injection.

