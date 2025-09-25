---
tags:
  - Git
  - Versioning
---

# GitHub Actions – Submodule Sync Debugging Log

---
## Diff detections worked — but push failed with 403 (access_denied_to_user)

What was seen:

```bash
Permission to Alx-a-cod/Folio.git denied to github-actions[bot]. fatal: unable to access 'https://github.com/Alx-a-cod/Folio.git/': The requested URL returned error: 403
```

**Root cause:**
- Attempts to push directly to protected `main` or push with an identity that isn’t allowed throws 403. 
  Branch-protection or repo settings prevented `github-actions[bot]` and default token from pushing.

<span style="color: #8392a4">Why?</span>
- Often the runner tries to push as `github-actions[bot]` despite having a PAT in secrets unless the PAT is used properly.

**Fix paths**:

- Two common approaches:

<span style="color: #e5a216">A —</span> Using `GITHUB_TOKEN` with explicit permissions
    Adding at top of workflow:
    `permissions:   contents: write`
`-->` This allows `GITHUB_TOKEN` to push (if branch protection permits pushes from workflows). Then ==push to a non-protected branch== (NOT MAIN!) and open a PR.

<span style="color: #e5a216">B —</span> Use a PAT (safer for protected `main`)
1. Create PAT (repo scope) and add it to repo secrets as `PAT_TOKEN`.
2. Use the PAT for checkout/push and gh auth:

```bash
# checkout using PAT so pushes use it
uses: actions/checkout@v3
with:
  token: ${{ secrets.PAT_TOKEN }}

# push using PAT
git push https://$PAT_TOKEN@github.com/${{ github.repository }} $BRANCH
# authenticate gh:
echo "$PAT_TOKEN" | gh auth login --with-token
```

---
## 403 persisted after PAT — why?

What was seen:
- Even after adding PAT, pushes still returned 403.

**Root cause:**
- PAT not actually used by the git client (checkout step / environment not configured), `actions/checkout` used default GITHUB_TOKEN instead of PAT.
- `git push` URL not using the PAT,  `gh` not authenticated with PAT before PR creation.

**Fix**
- Ensured `actions/checkout` is called with `token: ${{ secrets.PAT_TOKEN }}` _and_ explicit push using the PAT URL as shown above.
- Authenticated `gh` with the PAT before creating PRs:
  `echo "$PAT_TOKEN" | gh auth login --with-token`

---
## Final robust solution

<span style="color: #8392a4">Design choices made:</span>

- Updated sub-modules to `origin/main` with `git -C <sub> fetch` + `git -C <sub> checkout origin/main`.
- Used a <span style="color: #e5a216">dedicated branch</span> (`submodule-sync-update`) instead of pushing to `main`.
- Commit sub-module reference changes and pushed branch using a token that can be writ (`GITHUB_TOKEN` with `permissions: contents: write` _and_ pushing to ==non-protected branch==).
- Created PR automatically using `gh` after authenticating with token.
- Used `git diff --ignore-cr-at-eol` to avoid CRLF noise.
- Ensured YAML used **LF endings** and a **safe bash** style.
    

**Core final snippet (conceptual)**

```yaml
name: Pro Submodule Sync Checker

on:
  schedule:
    - cron: '0 9 * * *'  # Daily at 09:00 CET
  workflow_dispatch:

permissions:
  contents: write  # Allows pushing commits using GITHUB_TOKEN

jobs:
  check-submodules:
    runs-on: ubuntu-latest
    env:
      PR_BRANCH: submodule-sync-update
      PR_TITLE: "Auto-update submodule references"
      PR_BODY: "This PR updates the submodules to the latest commits from main."

    steps:
      # 1 Checkout main repo without submodules
      - name: Checkout main repo
        uses: actions/checkout@v3
        with:
          submodules: false
          fetch-depth: 0

      # 2️ Initialize submodules and fetch latest main
      - name: Update submodules
        run: |
          submodules=("Csharp_Algoritms" "CodingVault" "TasteTest" "An-Egg-" "ArtiFact")
          for sub in "${submodules[@]}"; do
            git submodule init $sub || echo "$sub already initialized"
            git submodule update --init --recursive $sub
            git -C $sub fetch origin main
            git -C $sub checkout origin/main
          done

      # 3️ Show submodule status
      - name: Submodule status
        run: git submodule status

      # 4️ Check if submodules changed
      - name: Check for submodule changes
        id: check_submodules
        run: |
          set -e
          CHANGES=false
          git diff --submodule=log --ignore-cr-at-eol --exit-code || CHANGES=true
          echo "changes=$CHANGES" >> $GITHUB_OUTPUT

      # 5️ Commit and push updates using GITHUB_TOKEN
      - name: Commit and push updates
        if: steps.check_submodules.outputs.changes == 'true'
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"

          # Checkout or reset branch
          git checkout -B $PR_BRANCH
          git add .
          git commit -m "$PR_TITLE" || echo "No changes to commit"

          # Use GITHUB_TOKEN for authentication automatically
          git push origin $PR_BRANCH --force

      # 6️ Create Pull Request if none exists
      - name: Create Pull Request
        if: steps.check_submodules.outputs.changes == 'true'
        run: |
          # Authenticate gh CLI using GITHUB_TOKEN
          echo $GITHUB_TOKEN | gh auth login --with-token

          # Skip if PR already exists
          EXISTING_PR=$(gh pr list --head $PR_BRANCH --json number --jq '.[0].number')
          if [ -z "$EXISTING_PR" ]; then
            gh pr create \
              --title "$PR_TITLE" \
              --body "$PR_BODY" \
              --head $PR_BRANCH \
              --base main \
              --repo ${{ github.repository }}
          else
            echo "PR already exists: #$EXISTING_PR, skipping creation."
          fi
```

---

### Quick debugging checklist

-  `git submodule status` → note hashes

-  `git -C <sub> fetch origin` and `git -C <sub> log origin/main -1`

-  If local submodule behind → `git -C <sub> checkout origin/main` and `git add <sub>` + commit in root

-  If workflow failing with 403 → check `permissions:` and branch protection / permissions OR use PAT

-  If “already exists” error → remove `git submodule add` from CI

-  If “unexpected end of file” in runner logs → convert YAML to LF and shorten inline ifs

-  Always normalize or ignore CRLF in diffs: `--ignore-cr-at-eol` (or set `git config core.autocrlf false` before diff)

---

## 💡 Lessons learned / Best practices (short)

- <span style="color: #e5a216">NEVER</span> `git submodule add` in CI;  initialize/update only.

- Push to a ==branch + open PR== rather than pushing straight to protected `main`.
  `Bad practice:` actions shouldn’t force-push to protected branches.

- Prefer `actions/checkout token:` 
	- The workflow had no `permissions: contents: write` set at the top.
	- By default, `GITHUB_TOKEN` only has **read-only** permissions in some contexts.
	- Push failed because bot account had no write access.

- Normalize line endings or use `--ignore-cr-at-eol` to avoid noise.

- <span style="color: #e5a216">Log which submodules changed</span> in the PR description — makes reviews fast.

- **Automate idempotently**: update the same branch each run instead of creating new branches every time.

### ✅ Final Solution

1. <span style="color: #8392a4">Switched from PAT to GitHub’s built-in `GITHUB_TOKEN`.</span>
 - Safer: scoped automatically to repo.
 - No need to manage or rotate tokens.
 
1. Added correct permissions at the top of the workflow:
```yaml
 permissions:
   contents: write
```