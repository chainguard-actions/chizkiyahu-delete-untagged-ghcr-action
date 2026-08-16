<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v5.0.0** was hardened automatically. 16 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple ${{ inputs.* }} and ${{ github.action_path }} expressions are directly interpolated inside run: shell command strings in action.yml. This allows an attacker who controls the calling workflow to inject arbitrary shell commands. Offending lines include: `args=( "--token" "${{ inputs.token }}" )`, `args+=( "--repository_owner" "${{ inputs.repository_owner }}" )`, `if [[ -n "${{ inputs.repository }}" ]]`, `args+=( "--repository" "${{ inputs.repository }}" )`, `if [[ -n "${{ inputs.package_name }}" ]]`, `args+=( "--package_names" "${{ inputs.package_name }}" )`, `args+=( "--untagged_only" "${{ inputs.untagged_only }}" )`, `args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )`, `args+=( "--owner_type" "${{ inputs.owner_type }}" )`, and `python ${{ github.action_path }}/clean_ghcr.py`. Also, `pip install -r ${{ github.action_path }}/requirements.txt` interpolates ${{ github.action_path }} directly. All inputs should be passed via env: variables and referenced as quoted shell variables instead.

Locations:

- `action.yml:47`
- `action.yml:51`
- `action.yml:52`
- `action.yml:53`
- `action.yml:55`
- `action.yml:56`
- `action.yml:58`
- `action.yml:59`
- `action.yml:60`
- `action.yml:61`
- `action.yml:63`

### script-injection (severity: high)

Sub-rule (a): ${{ github.sha }} is directly interpolated inside a run: shell command string in linter.yml. The offending line is: `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV`. While github.sha is not attacker-controlled in the same way as inputs, any ${{ ... }} expression inside a run: block is a script-injection risk as the value is substituted before the shell parses the command. It should be passed via an env: variable instead.

Locations:

- `.github/workflows/linter.yml:19`

### github-env-injection (severity: high)

The run: block in linter.yml writes a value derived from ${{ github.sha }} directly to $GITHUB_ENV without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). The offending line is: `echo "GITHUB_SHA_SHORT=$(echo ${{ github.sha }} | cut -c 1-6)" >> $GITHUB_ENV`. An attacker who can influence github.sha (or any future refactoring that substitutes a more controllable value) could inject newlines to set arbitrary environment variables for subsequent steps.

Locations:

- `.github/workflows/linter.yml:19`

### unpinned-uses (severity: high)

action.yml references actions/setup-python@v5 using a mutable version tag instead of a full 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to a malicious commit. Failing reference: `uses: actions/setup-python@v5`.

Locations:

- `action.yml:43`

### unpinned-uses (severity: high)

linter.yml references multiple actions using mutable version tags instead of full 40-character commit SHAs. Failing references: `uses: actions/checkout@v3` (line 15), `uses: actions/setup-python@v5` (line 21), `uses: peter-evans/create-pull-request@v4` (line 34). These are vulnerable to supply-chain attacks if any tag is moved to a malicious commit.

Locations:

- `.github/workflows/linter.yml:15`
- `.github/workflows/linter.yml:21`
- `.github/workflows/linter.yml:34`

### unpinned-uses (severity: high)

test.yml references multiple actions using mutable version tags instead of full 40-character commit SHAs. Failing references: `uses: actions/checkout@v4` (multiple jobs), `uses: docker/setup-buildx-action@v3`, `uses: docker/login-action@v3`, `uses: ASzc/change-string-case-action@v6`, `uses: docker/build-push-action@v5`. These are vulnerable to supply-chain attacks if any tag is moved to a malicious commit.

Locations:

- `.github/workflows/test.yml:17`
- `.github/workflows/test.yml:30`
- `.github/workflows/test.yml:33`
- `.github/workflows/test.yml:36`
- `.github/workflows/test.yml:39`
- `.github/workflows/test.yml:44`
- `.github/workflows/test.yml:59`
- `.github/workflows/test.yml:63`
- `.github/workflows/test.yml:66`
- `.github/workflows/test.yml:69`
- `.github/workflows/test.yml:74`

### permissions (severity: medium)

missing-permissions: linter.yml has no top-level `permissions:` key and the single job `formater` also has no job-level `permissions:` key. Without explicit permissions, the workflow runs with the default token permissions which may be overly broad (e.g., write access to contents and packages). A minimal permissions block should be added.

Locations:

- `.github/workflows/linter.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.token }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:54`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository_owner }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:55`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.repository }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package_name }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:59`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.package_name }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.untagged_only }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:62`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.except_untagged_multiplatform }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:63`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.owner_type }}" appears directly in run: block of step "Run action"; move to env: map

Locations:

- `action.yml:64`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, github-env-injection, unpinned-uses, permissions

**Notes:**

Fixed all findings across action.yml, linter.yml, and test.yml:

1. action.yml - script-injection/static-inline-injection: Moved all ${{ inputs.* }} and ${{ github.action_path }} expressions to env: blocks in both the 'Install Python dependencies' and 'Run action' steps. Shell script now uses plain env vars ($INPUT_TOKEN, $INPUT_REPOSITORY_OWNER, $INPUT_REPOSITORY, $INPUT_PACKAGE_NAME, $INPUT_UNTAGGED_ONLY, $INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM, $INPUT_OWNER_TYPE, $ACTION_PATH).

2. action.yml - unpinned-uses: Pinned actions/setup-python@v5 to full SHA a26af69be951a213d495a4c3e4e4022e16d87065.

3. linter.yml - script-injection: Moved ${{ github.sha }} to env var GITHUB_SHA_VALUE.

4. linter.yml - github-env-injection: Sanitized the SHA value with printf + tr -d '\n\r' before writing to $GITHUB_ENV.

5. linter.yml - unpinned-uses: Pinned actions/checkout@v3, actions/setup-python@v5, and peter-evans/create-pull-request@v4 to full SHAs.

6. linter.yml - permissions: Added top-level permissions block with contents: write and pull-requests: write.

7. test.yml - unpinned-uses: Pinned all action references (actions/checkout@v4, docker/setup-buildx-action@v3, docker/login-action@v3, ASzc/change-string-case-action@v6, docker/build-push-action@v5) to full commit SHAs.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed all five script injection instances in .github/workflows/test.yml. In each affected run: block (jobs: clean_untagged_pkgs1, clean_untagged_pkgs2, delete_package, delete_multiple_packages, clean_repo), moved `${{ steps.deleted-action.outputs.num_deleted }}` out of the shell string and into a step-level `env:` block as `NUM_DELETED`. The shell scripts now reference `$NUM_DELETED` as a plain environment variable, eliminating the risk of template-engine injection before the shell sees the string.

