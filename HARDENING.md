<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.1.1** was hardened automatically. 18 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The 'Run action' step in action.yml directly interpolates multiple ${{ inputs.* }} expressions and ${{ github.action_path }} inside a run: shell script. This is a script-injection vulnerability (sub-rule a): the YAML template substitution happens before the shell parses the string, allowing an attacker-controlled input to inject arbitrary shell commands. Offending lines include: `args=( "--token" "${{ inputs.token }}" )`, `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`, `if [[ -n "${{ inputs.repository }}" ]]`, `if [[ -n "${{ inputs.package_name }}" ]]`, `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`, `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`, `args+=( "--with_sigs" "${{ inputs.with_sigs }}")`, `args+=( "--owner_type" "${{ inputs.owner_type }}" )`, and `python ${{ github.action_path }}/clean_ghcr.py`. All inputs should be passed via env: variables and referenced as quoted shell variables.

Locations:

- `action.yml:56`

### script-injection (severity: high)

The 'Cut sha for PR branch name' step in linter.yml directly interpolates ${{ github.sha }} and ${{ env.GITHUB_SHA_SHORT }} inside run: shell commands (sub-rule a). Any ${{ ... }} expression inside a run: block is a script-injection risk because YAML template substitution occurs before the shell parses the string. Offending lines: `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV` and `echo ${{ env.GITHUB_SHA_SHORT }}`.

Locations:

- `.github/workflows/linter.yml:25`

### github-env-injection (severity: high)

The 'Cut sha for PR branch name' step in linter.yml writes a value derived from ${{ github.sha }} directly to $GITHUB_ENV without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). The line `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV` allows a newline-containing value to inject additional environment variables or override existing ones. Although github.sha is typically safe, the rules require sanitization for all github.* context values written to special environment files.

Locations:

- `.github/workflows/linter.yml:25`

### unpinned-uses (severity: high)

action.yml references three actions using mutable version tags instead of immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved: `actions/setup-python@v6` (line 46), `docker/setup-buildx-action@v4` (line 49), `docker/login-action@v4` (line 51).

Locations:

- `action.yml:46`
- `action.yml:49`
- `action.yml:51`

### unpinned-uses (severity: high)

linter.yml references actions using mutable version tags instead of immutable SHA digests: `actions/checkout@v6` and `actions/setup-python@v6`.

Locations:

- `.github/workflows/linter.yml:21`
- `.github/workflows/linter.yml:27`

### unpinned-uses (severity: high)

reusable.yml references actions using mutable version tags instead of immutable SHA digests: `actions/checkout@v6`, `docker/setup-buildx-action@v4`, `docker/login-action@v4`, and `docker/build-push-action@v7`. (Note: sigstore/cosign-installer and docker/metadata-action are correctly pinned to SHA digests.)

Locations:

- `.github/workflows/reusable.yml:27`
- `.github/workflows/reusable.yml:35`
- `.github/workflows/reusable.yml:38`
- `.github/workflows/reusable.yml:55`

### unpinned-uses (severity: high)

signed.yml references `actions/checkout@v6` using a mutable version tag instead of an immutable SHA digest. This appears in multiple jobs (reset, delete_package_with_signature, clean_repo).

Locations:

- `.github/workflows/signed.yml:33`
- `.github/workflows/signed.yml:60`
- `.github/workflows/signed.yml:75`

### unpinned-uses (severity: high)

test.yml references actions using mutable version tags instead of immutable SHA digests: `actions/checkout@v6` (multiple jobs), `docker/setup-buildx-action@v4`, and `docker/login-action@v4`.

Locations:

- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:57`
- `.github/workflows/test.yml:59`
- `.github/workflows/test.yml:68`
- `.github/workflows/test.yml:82`
- `.github/workflows/test.yml:97`

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

Fixed all findings across action.yml and .github/workflows/*.yml:

1. action.yml - script-injection/static-inline-injection: Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions from run: blocks into env: blocks. The run: block now uses plain shell variables ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, $INPUT_REPOSITORY, $INPUT_PACKAGE_NAME, $INPUT_UNTAGGED_ONLY, $INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, $INPUT_WITH_SIGS, $INPUT_OWNER_TYPE, $ACTION_PATH).

2. action.yml - unpinned-uses: Pinned actions/setup-python@v6 → ece7cb06caefa5fff74198d8649806c4678c61a1, docker/setup-buildx-action@v4 → bb05f3f5519dd87d3ba754cc423b652a5edd6d2c, docker/login-action@v4 → af1e73f918a031802d376d3c8bbc3fe56130a9b0.

3. linter.yml - script-injection: Moved ${{ github.sha }} into env: block (FULL_SHA), run: block uses $FULL_SHA.

4. linter.yml - github-env-injection: Added sanitization with `printf '%s' "$FULL_SHA" | tr -d '\n\r'` before writing to $GITHUB_ENV.

5. linter.yml - unpinned-uses: Pinned actions/checkout@v6 → df4cb1c069e1874edd31b4311f1884172cec0e10, actions/setup-python@v6 → ece7cb06caefa5fff74198d8649806c4678c61a1.

6. reusable.yml - unpinned-uses: Pinned actions/checkout@v6, docker/setup-buildx-action@v4, docker/login-action@v4, docker/build-push-action@v7 to their respective SHAs.

7. signed.yml - unpinned-uses: Pinned all 3 occurrences of actions/checkout@v6 to SHA df4cb1c069e1874edd31b4311f1884172cec0e10.

8. test.yml - unpinned-uses: Pinned all 8 occurrences of actions/checkout@v6, plus docker/setup-buildx-action@v4 and docker/login-action@v4 to their respective SHAs.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed 10 script-injection instances across 3 workflow files:
1. hardened/action/.github/workflows/linter.yml (line 38): Replaced `${{ env.IS_FORMATTED }}` in echo command with the local shell variable `$is_formatted` already set in the same run block.
2. hardened/action/.github/workflows/signed.yml (lines 68, 84): Moved `${{ steps.deleted-action.outputs.num_deleted }}` out of both run: blocks into step-level `env: NUM_DELETED:` declarations; shell scripts now use `$NUM_DELETED`.
3. hardened/action/.github/workflows/test.yml (lines 80, 97, 113, 128, 145, 185, 202): Moved `${{ steps.deleted-action.outputs.num_deleted }}` out of all 7 run: blocks into step-level `env: NUM_DELETED:` declarations; shell scripts now use `$NUM_DELETED`.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted `${DIGEST}` variable in the 'Sign the published Docker image' step in `.github/workflows/reusable.yml`. Changed `{}@${DIGEST}` to `{}@"${DIGEST}"` so the digest value is properly double-quoted, preventing shell metacharacter injection.

