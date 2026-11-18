<!-- markdownlint-disable -->

# Hardening Report: chizkiyahu--delete-untagged-ghcr-action/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **chizkiyahu--delete-untagged-ghcr-action/v6.0.0** was hardened automatically. 14 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the 'Install Python dependencies' step puts an expression straight into its run: script: `pip install -r ${{ github.action_path }}/requirements.txt`. Move it into env (or use $GITHUB_ACTION_PATH) and double-quote the variable.

Locations:

- `action.yml:53`

### script-injection (severity: high)

Rule (a): the 'Run action' step puts several expressions straight into its run: script, for example `args=( "--token" "${{ inputs.token }}" )`, `if [[ -n "${{ inputs.repository }}" ]]`, `args+=( "--package_names" "${{ inputs.package_name }}" )`, `args+=( "--owner_type" "${{ inputs.owner_type }}" )` and `python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"`. These include inputs.token, inputs.repository_owner, inputs.repository, inputs.package_name, inputs.untagged_only, inputs.except_untagged_multiplatform, inputs.with_sigs and inputs.owner_type. Pass each one through env: and refer to it as a double-quoted shell variable.

Locations:

- `action.yml:66`
- `action.yml:80`

### unpinned-uses (severity: high)

Three composite steps use a version tag instead of a full commit SHA: `uses: actions/setup-python@v5`, `uses: docker/setup-buildx-action@v3`, `uses: docker/login-action@v3`.

Locations:

- `action.yml:48`
- `action.yml:55`
- `action.yml:57`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 814bdac..f7e3216 100644
--- a/action.yml
+++ b/action.yml
@@ -7,10 +7,8 @@ branding:
 inputs:
   token:
     description: >
-      Personal access token (PAT) used to fetch the repository. The PAT is configured
-      with the local git config, which enables your scripts to run authenticated git
-      commands. The post-job step removes the PAT.
-      needs delete:packages permissions
+      Personal access token (PAT) used to fetch the repository. The PAT is configured with the local git config, which enables your scripts to run authenticated git commands. The post-job step removes the PAT. needs delete:packages permissions
+
     required: true
   repository_owner:
     description: 'The repository owner name'
@@ -34,27 +32,43 @@ inputs:
     default: false
     required: false
   owner_type:
-    description : "Owner type (org or user)"
+    description: "Owner type (org or user)"
     required: true
 outputs:
   num_deleted:
     description: 'Number of package versions that were deleted during the run.'
-    value:  ${{ steps.delete-untagged-ghcr-action.outputs.num_deleted }}
-
+    value: ${{ steps.delete-untagged-ghcr-action.outputs.num_deleted }}
 runs:
   using: "composite"
   steps:
     - name: Setup Python
       uses: actions/setup-python@v5
       with:
         python-version: '3.11'
     - name: Install Python dependencies
       shell: bash
-      run: pip install -r ${{ github.action_path }}/requirements.txt
+      run: pip install -r "$GITHUB_ACTION_PATH/requirements.txt"
     - name: Set up Docker Buildx
       uses: docker/setup-buildx-action@v3
     - name: Login to GitHub Container Registry with PAT_TOKEN
       uses: docker/login-action@v3
       with:
         registry: ghcr.io
         username: ${{ github.actor }}
@@ -62,18 +76,27 @@ runs:
     - name: Run action
       id: delete-untagged-ghcr-action
       shell: bash
+      env:
+        INPUT_TOKEN: ${{ inputs.token }}
+        INPUT_REPOSITORY_OWNER: ${{ inputs.repository_owner }}
+        INPUT_REPOSITORY: ${{ inputs.repository }}
+        INPUT_PACKAGE_NAME: ${{ inputs.package_name }}
+        INPUT_UNTAGGED_ONLY: ${{ inputs.untagged_only }}
+        INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM: ${{ inputs.except_untagged_multiplatform }}
+        INPUT_WITH_SIGS: ${{ inputs.with_sigs }}
+        INPUT_OWNER_TYPE: ${{ inputs.owner_type }}
       run: |
-        args=( "--token" "${{ inputs.token }}" )
-        args+=( "--repository_owner" "${{ inputs.repository_owner }}" )
-        if [[ -n "${{ inputs.repository }}" ]]; then
-          args+=( "--repository" "${{ inputs.repository }}" )
+        args=( "--token" "$INPUT_TOKEN" )
+        args+=( "--repository_owner" "$INPUT_REPOSITORY_OWNER" )
+        if [[ -n "$INPUT_REPOSITORY" ]]; then
+          args+=( "--repository" "$INPUT_REPOSITORY" )
         fi
-        if [[ -n "${{ inputs.package_name }}" ]]; then
-          args+=( "--package_names" "${{ inputs.package_name }}" )
+        if [[ -n "$INPUT_PACKAGE_NAME" ]]; then
+          args+=( "--package_names" "$INPUT_PACKAGE_NAME" )
         fi
-        args+=( "--untagged_only" "${{ inputs.untagged_only }}" )
-        args+=( "--except_untagged_multiplatform" "${{ inputs.except_untagged_multiplatform }}" )
-        args+=( "--with_sigs" "${{ inputs.with_sigs }}")
-        args+=( "--owner_type" "${{ inputs.owner_type }}" )
+        args+=( "--untagged_only" "$INPUT_UNTAGGED_ONLY" )
+        args+=( "--except_untagged_multiplatform" "$INPUT_EXCEPT_UNTAGGED_MULTIPLATFORM" )
+        args+=( "--with_sigs" "$INPUT_WITH_SIGS")
+        args+=( "--owner_type" "$INPUT_OWNER_TYPE" )
         echo "args: ${args[@]}"
-        python ${{ github.action_path }}/clean_ghcr.py "${args[@]}"
+        python "$GITHUB_ACTION_PATH/clean_ghcr.py" "${args[@]}"

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, static-inline-injection, prior-fix

**Notes:**

I fixed every finding in action.yml.

- **Pinned actions:** the three actions are now pinned to full commit SHAs, with the original tag kept as a comment: actions/setup-python@v5, docker/setup-buildx-action@v3 and docker/login-action@v3.
- **Install step:** `${{ github.action_path }}` is replaced with "$GITHUB_ACTION_PATH" in double quotes.
- **Run action step:** the 8 inputs now go through `env:` as INPUT_* variables and are used as double-quoted shell variables. The script's path also uses "$GITHUB_ACTION_PATH" now.
- **Previous harden:** its security changes are back in place. I didn't redo its formatting-only YAML changes.

The arguments passed to clean_ghcr.py are the same as upstream's, so behaviour shouldn't change. I haven't run the action.

