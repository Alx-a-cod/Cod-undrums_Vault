---
tags:
  - Git
  - Versioning
---

# GitHub Actions – Submodule Sync Debugging Log

### ❌ Initial Problem

- Tried to create a workflow that syncs submodules and pushes updates automatically.

- **Error:** Locally `git submodule status` showed an older hash than the GitHub repo.

💡 Vedi la soluzione → [[GithHub Submodules Sync - Soluzione]]

```yaml

name: Submodule Sync Checker

on:
  schedule:
    - cron: '0 9 * * *' # Runs daily at 09:00 CET
  workflow_dispatch: # Allows manual trigger

jobs:
  check-submodules:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Folio with submodules
        uses: actions/checkout@v3
        with:
          submodules: true

      - name: Update submodules to latest remote commits
        run: |
          git submodule update --remote

      - name: Check for submodule changes
        run: |
          if ! git diff --quiet; then
            echo "::warning::One or more submodules are out of sync. Please commit updated references to Folio."
          else
            echo "Great! All submodules are up to date."
          fi


```

- **Root cause:** The workflow _checked out_ the 'Folio' repo with sub-modules and then ran:
``` bash
git submodule update --remote
```

but when running a workflow in GitHub actions, by default: 

1. You are in a **detatched** HEAD STATE (pointing to the commit that triggered the workflow).

2. `git submodules update --remote` updates the sub-module ==locally== but does NOT fetch the latest commit from the remote ==unless the sub-module's remote tracking branch is configured==. 
  > Submodule in Folio was not configured to track branch (or workflow didn’t checkout the tracked branch).

3. Even IF it fetches the latest commit, the <span style="color: #fa8231">workflow doesn't push the update back to the main repo</span>, so it looks like nothing changed. 

#### First Fix / commands: 
```bash
# inspected submodules (from Folio root)
git submodule status

# entered correct submodule folder (use exact name from status)
cd Csharp_Algoritms

# saw what origin/main points to
git fetch origin
git log origin/main -1 --oneline

# updated submodule to remote main
git checkout main        # attached to branch if needed
git pull origin main     # then also tried to: git checkout origin/main if detached
cd ..
git add Csharp_Algoritms
git commit -m "Update Csharp_Algoritms submodule to latest"
git push origin main
```

#### ❌ Second error: during CI  `fatal: 'X' already exists and is not a valid git repo`

- Manually triggered action responded with: 
``` bash
fatal: 'Csharp_Algoritms' already exists and is not a valid git repo
```

**Root cause**:
- Workflow used `git submodule add` while the runner already checked out sub-modules (or the folder exists). `submodule add` must not run where the folder is present.
    

**Fix**:
- Fixed `submodule add` in CI. Use `git submodule update --init --recursive` and then `git -C <sub> fetch && git -C <sub> checkout origin/<branch>`.
``` yaml

      # 2️⃣ Initialize submodules if not already initialized, then fetch latest from main
      - name: Update submodules to latest main
        run: |
          # Ensure each submodule is initialized
          git submodule init Csharp_Algoritms || echo "Already initialized"
          git submodule init CodingVault || echo "Already initialized"
          git submodule init TasteTest || echo "Already initialized"
          git submodule init An-Egg- || echo "Already initialized"
          git submodule init ArtiFact || echo "Already initialized"

          # Update submodules recursively
          git submodule update --init --recursive

          # Fetch and checkout latest commit from main branch for each submodule
          git -C Csharp_Algoritms fetch origin main && git -C Csharp_Algoritms checkout origin/main
          git -C CodingVault fetch origin main && git -C CodingVault checkout origin/main
          git -C TasteTest fetch origin main && git -C TasteTest checkout origin/main
          git -C An-Egg- fetch origin main && git -C An-Egg- checkout origin/main
          git -C ArtiFact fetch origin main && git -C ArtiFact checkout origin/main

      # 3️⃣ Show current submodule status
      - name: Show submodule status
        run: git submodule status

      # 4️⃣ Check for submodule changes
      - name: Check for submodule changes
        id: check_submodules
        run: |
          if ! git diff --quiet --submodule=log; then
            echo "::warning::One or more submodules are out of sync!"
            git diff --submodule=log
            echo "changes=true" >> $GITHUB_OUTPUT
          else
            echo "All submodules are up to date."
            echo "changes=false" >> $GITHUB_OUTPUT

      # 5️⃣ Optional: commit updated submodule references
      - name: Commit updated submodules
        if: steps.check_submodules.outputs.changes == 'true'
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add .
          git commit -m "Auto-update submodule references" || echo "No changes to commit"
          git push origin main || echo "Push failed (maybe branch protection)"

```

thus kept: 
1.  **explicit URLs** for clarity.
2. Avoid strategy of `git submodule add` so the workflow **wouldn’t fail if submodules existed**.
3. Fetches the **latest `main` commit** for each submodule.
4. Logs changes clearly with `git diff --submodule=log`.
5. Optional commit step pushes updates automatically.
 
#### ❌ Third error: Windows-style CRLF line endings
```bash 
/home/runner/work/_temp/af2562e1-3168-4770-9cce5f314533.sh: line 8: syntax error: unexpected end of file
Error: Process completed with exit code 2.
```
- **Root cause:**  
    - workflow file uses **Windows-style CRLF line endings**, GitHub Actions runners on Linux **see an unexpected end of file**.
        - YAML itself is fine, but when bash reads it in the `run:` block, CRLF causes a syntax error.
    
- **Fix**: 
    - rewrote that `Check for submodule changes` step in a slightly safer way that wouldn't break on line endings at all, avoiding the bash EOF problem to work reliably on Linux runners and **LF line endings**
        -  Uses `set -e` to make bash stop on errors **only where necessary**.
        - Stores the diff result in a **variable** (`CHANGES`) instead of relying on inline if-else that bash sometimes misparses with CRLF.
        - Always writes a clean `changes=` output to `$GITHUB_OUTPUT`.
    
```bash
      # 4️⃣ Check for submodule changes (safe version)
      - name: Check for submodule changes
        id: check_submodules
        run: |
          set -e
          CHANGES=false
          git diff --submodule=log --exit-code || CHANGES=true
          if [ "$CHANGES" = true ]; then
            echo "::warning::One or more submodules are out of sync!"
            git diff --submodule=log
          else
            echo "All submodules are up to date."
          fi
          echo "changes=$CHANGES" >> $GITHUB_OUTPUT

```

#### ❌ Fourth error: detecting submodule changes correctly without auto-update

- The workflow failed with:  
``` bash
 Warning: One or more submodules are out of sync!
```

**Root cause:**
-  GitHub Actions checks `if: steps.check_submodules.outputs.changes == 'true'`, then it commits & pushes the **updated sub-module references** in Folio’s main repo.
  
##### Why it may not have pushed:

1. *Branch protection rules* on `main`
    - If `main` is protected, GH Actions can’t push automatically.
    - <span style="color: #8392a4">Solution</span>: tried disabling push protection temporarily for the bot, didn't like it. Started investigating push action to a separate branch and make a PR.

2. **GITHUB_TOKEN permissions**
    - By default, `GITHUB_TOKEN` has `write` access to the repo, but *branch protections* can override it.

**Fix**:
- allowed bot to push on main via <span style="color: #8392a4">personal access token (PAT)</span> with repo write permissions. 

> [!note] To generate a Personal Access Token (PAT) on GitHub:
>  *Step 1: Go to Developer Settings*
> 1. Github Profile Picture (top-right corner) → `Settings`
> 2. Scroll down to `'Developer Settings'`
> *Step 2: Create a new Token*
> 3. Click `'Personal Access Token'` → Tokens (classic)
> 4. Click **Generate new token** → Generate new token (classic)
> 5. Give it a name, e.g., `PippoPluto`
> 6. Set **Expiration** (choose what’s convenient, e.g., 90 days or no expiration)
> 7. Under **Scopes**, check **repo** (gives full read/write access to repositories)
> 8. Click **Generate token**
> *Step 3: Copy the token*
> 9. **Copy it immediately** (you won’t be able to see it again)
> *Step 4: Add it as a secret to repository*
> 10. Go to `Folio repo → Settings → Secrets and variables → Actions → New repository secret`
> 11. Name: `PAT_TOKEN`
> 12. Secret / Value: paste the **PAT string** you just copied.
> 13. Click **Add secret**.

```yaml
name: Pro Submodule Sync Checker

on:
  schedule:
    - cron: '0 9 * * *'  # Daily at 09:00 CET
  workflow_dispatch:

jobs:
  check-submodules:
    runs-on: ubuntu-latest
    env:
      PAT_TOKEN: ${{ secrets.PAT_TOKEN }}  # Use a personal access token with repo write permissions

    steps:
      # 1️⃣ Checkout Folio without submodules
      - name: Checkout Folio
        uses: actions/checkout@v3
        with:
          submodules: false
          fetch-depth: 0

      # 2️⃣ Initialize submodules and fetch latest main commits
      - name: Update submodules to latest main
        run: |
          submodules=("Csharp_Algoritms" "CodingVault" "TasteTest" "An-Egg-" "ArtiFact")
          for sub in "${submodules[@]}"; do
            git submodule init $sub || echo "$sub already initialized"
            git submodule update --init --recursive $sub
            git -C $sub fetch origin main
            git -C $sub checkout origin/main
          done

      # 3️⃣ Show submodule status
      - name: Show submodule status
        run: git submodule status

      # 4️⃣ Check for submodule changes
      - name: Check for submodule changes
        id: check_submodules
        run: |
          set -e
          CHANGES=false
          git diff --submodule=log --exit-code || CHANGES=true
          if [ "$CHANGES" = true ]; then
            echo "::warning::One or more submodules are out of sync!"
            git diff --submodule=log
          else
            echo "All submodules are up to date."
          fi
          echo "changes=$CHANGES" >> $GITHUB_OUTPUT

      # 5️⃣ Commit & push updated submodule references automatically
      - name: Commit updated submodules
        if: steps.check_submodules.outputs.changes == 'true'
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add .
          git commit -m "Auto-update submodule references" || echo "No changes to commit"
          git push --force-with-lease https://$PAT_TOKEN@github.com/Alx-a-cod/Folio.git main
```

thus made it so: 

- <span style="color: #8392a4">Submodule update loop</span>
    - looping over the submodules, fetching `origin main`, and checkout `origin/main`.

- <span style="color: #8392a4">Push step</span>
    `git push https://$PAT_TOKEN@github.com/Alx-a-cod/Folio.git main`
    -  authentication to using PAT.
        
- <span style="color: #8392a4">Optional improvement:</span>
    - add `--force-with-lease`. Used only if expecting someone else might push simultaneously, to avoid conflicts but cool(?):
     `git push --force-with-lease https://$PAT_TOKEN@github.com/Alx-a-cod/Folio.git main`
    
- <span style="color: #8392a4">Line endings / LF safety</span>
    - Using `set -e` and a multi-line `run: |` block in bash ensures no more Linux-ending crashes.
        
- <span style="color: #8392a4">Fetch depth</span>
    - `fetch-depth: 0` is correct;  to see full history to see diffs.
 
 - `Permission to Alx-a-cod/Folio.git denied to github-actions[bot].`

```yaml
name: Update Submodules

on:
  schedule:
    - cron: '0 9 * * *' # Daily at 09:00 CET
  workflow_dispatch: # Manual trigger

jobs:
  check-submodules:
    runs-on: ubuntu-latest
    env:
      PAT_TOKEN: ${{ secrets.PAT_TOKEN }} # Personal Access Token with repo write permissions
      PR_BRANCH: submodule-sync-update
      PR_TITLE: "Auto-update submodule references"
      PR_BODY: "This PR updates the submodules to the latest commits from main."

    steps:
      # 1️ Checkout Folio without submodules initially
      - name: Checkout Folio
        uses: actions/checkout@v3
        with:
          submodules: false
          fetch-depth: 0

      # 2️ Initialize submodules and fetch latest commits from main
      - name: Update submodules to latest main
        run: |
          submodules=("Csharp_Algoritms" "CodingVault" "TasteTest" "An-Egg-" "ArtiFact")
          for sub in "${submodules[@]}"; do
            git submodule init $sub || echo "$sub already initialized"
            git submodule update --init --recursive $sub
            git -C $sub fetch origin main
            git -C $sub checkout origin/main
          done

      # 3️ Show current submodule status
      - name: Show submodule status
        run: git submodule status

      # 4️ Check for submodule changes (cross-platform safe)
      - name: Check for submodule changes
        id: check_submodules
        run: |
          set -e
          CHANGES=false
          # Ignore line ending differences (Windows vs Linux)
          git diff --submodule=log --ignore-cr-at-eol --exit-code || CHANGES=true
          echo "changes=$CHANGES" >> $GITHUB_OUTPUT

      # 5️ Commit updated submodules to a new branch and push
      - name: Commit updated submodules to new branch
        if: steps.check_submodules.outputs.changes == 'true'
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git checkout -b $PR_BRANCH
          git add .
          git commit -m "$PR_TITLE" || echo "No changes to commit"
          git push https://$PAT_TOKEN@github.com/${{ github.repository }} $PR_BRANCH --force

      # 6️ Create a Pull Request automatically
      - name: Create Pull Request
        if: steps.check_submodules.outputs.changes == 'true'
        run: |
          gh auth login --with-token <<< "$PAT_TOKEN"
          gh pr create \
            --title "$PR_TITLE" \
            --body "$PR_BODY" \
            --head $PR_BRANCH \
            --base main \
            --repo ${{ github.repository }}
```


#### ❌ Fifth error: Diff detections worked — but push failed with persisting 403 (access_denied_to_user)

- The workflow failed with:  
``` bash
 Permission to Alx-a-cod/Folio.git denied to github-actions[bot].
fatal: unable to access 'https://github.com/Alx-a-cod/Folio.git/': The requested URL returned error: 403
```

   Even after adding PAT, pushes still returned 403.