# Exercise 20: History Cleanup - The Nuclear Option

## 🎯 Objective
Learn advanced techniques to completely rewrite Git history, remove sensitive data, and clean up repository history.

## ⚠️ CRITICAL WARNING
**EXTREME DANGER ZONE!**
- These commands rewrite history
- Can destroy data permanently
- Should NEVER be used on shared repositories without team coordination
- Always backup before proceeding
- Requires force push
- Can break other people's work

**Only use these tools when:**
- Removing sensitive data (passwords, keys)
- Cleaning up large files accidentally committed
- Completely restructuring repository history
- You understand the consequences

## 📚 Concepts Covered
- `git filter-branch` (legacy)
- `git filter-repo` (modern)
- BFG Repo-Cleaner
- Removing sensitive data
- Removing large files
- Rewriting commit history
- Force pushing considerations
- Repository cleanup

## 📝 Preparation

```powershell
mkdir history-cleanup
cd history-cleanup
git init
```

## Part 1: Understanding the Problem

### Task 1: Accidentally Committed Sensitive Data
Simulate the problem:

```powershell
# Create file with sensitive data
@"
API_KEY=sk_live_secret_key_123456
DATABASE_PASSWORD=super_secret_password
AWS_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE
"@ | Out-File .env -Encoding UTF8

# Accidentally commit it!
git add .env
git commit -m "Add configuration"

# More commits on top
"Normal work" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "feat: add app logic"

"More work" | Out-File app.js -Encoding UTF8
git commit -am "feat: improve app"

# Oh no! Sensitive data is in history!
# Deleting file now doesn't help - it's in Git history
git rm .env
git commit -m "Remove .env file"

# But it's still in history!
git log --all -- .env
git show <commit-hash>:.env  # Still accessible!

# Need to rewrite history to remove it completely
```

### Task 2: Large File Bloat
Simulate large file problem:

```powershell
# Create "large" file
1..1000 | ForEach-Object { "Large data $_" } | Out-File large-file.bin -Encoding UTF8
git add large-file.bin
git commit -m "Add large file"

# More commits
"Normal work" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "feat: normal work"

# Remove large file
git rm large-file.bin
git commit -m "Remove large file"

# But repo is still large!
# File is in history
git count-objects -vH
# Size-pack shows size

# Need to purge from history
```

## Part 2: Filter-Branch (Legacy Method)

### Task 3: Remove File with Filter-Branch
**Note**: `filter-branch` is deprecated but still widely documented:

```powershell
# Remove .env from all history
git filter-branch --force --index-filter `
  "git rm --cached --ignore-unmatch .env" `
  --prune-empty --tag-name-filter cat -- --all

# Explanation:
# --index-filter: Rewrite index (staging area)
# --prune-empty: Remove empty commits
# --tag-name-filter cat: Update tags
# -- --all: All branches

# Check - .env should be gone from history
git log --all -- .env  # No results

# Clean up refs
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Check size
git count-objects -vH
```

### Task 4: Remove File from Subdirectory
```powershell
# Create structure
mkdir config
"secret" | Out-File config\secrets.txt -Encoding UTF8
git add config
git commit -m "Add secrets"

"work" | Out-File app.js -Encoding UTF8
git commit -am "More work"

# Remove secrets from history
git filter-branch --force --index-filter `
  "git rm --cached --ignore-unmatch config/secrets.txt" `
  --prune-empty -- --all

# Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

### Task 5: Rewrite Author Information
Change author name/email in history:

```powershell
# Fix author in all commits
git filter-branch --env-filter '
if [ "$GIT_COMMITTER_EMAIL" = "old@example.com" ]
then
    export GIT_COMMITTER_NAME="New Name"
    export GIT_COMMITTER_EMAIL="new@example.com"
    export GIT_AUTHOR_NAME="New Name"
    export GIT_AUTHOR_EMAIL="new@example.com"
fi
' --tag-name-filter cat -- --all
```

## Part 3: BFG Repo-Cleaner (Recommended)

### Task 6: Install BFG
BFG is faster and simpler than filter-branch:

```powershell
# Download BFG (Java required)
# From: https://rtyley.github.io/bfg-repo-cleaner/
# Download bfg.jar

# Or install via Scoop (Windows)
scoop install bfg

# Or manual download
# curl -L https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar -o bfg.jar
```

### Task 7: Remove Sensitive File with BFG
```powershell
# Simpler syntax than filter-branch!

# Clone repo as mirror
git clone --mirror history-cleanup bfg-backup.git

# Remove .env from history
java -jar bfg.jar --delete-files .env history-cleanup

# Or if installed via scoop:
bfg --delete-files .env history-cleanup

# Clean up
cd history-cleanup
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

### Task 8: Remove Files by Pattern
```powershell
# Remove all .env files
bfg --delete-files '*.env' history-cleanup

# Remove all .log files
bfg --delete-files '*.log' history-cleanup

# Remove specific folder
bfg --delete-folders 'secrets' history-cleanup

# Remove large files (over 100MB)
bfg --strip-blobs-bigger-than 100M history-cleanup
```

### Task 9: Replace Sensitive Strings
Replace passwords/keys in all files:

```powershell
# Create file with strings to replace
@"
PASSWORD1
SECRET_KEY_123
sk_live_abc123
"@ | Out-File passwords.txt -Encoding UTF8

# Replace them with ***REMOVED***
bfg --replace-text passwords.txt history-cleanup

# Or replace inline
bfg --replace-text 'PASSWORD1==>***REMOVED***' history-cleanup

# Check history
git log -p | Select-String "PASSWORD1"  # Should be gone
```

## Part 4: Git Filter-Repo (Modern Method)

### Task 10: Install Filter-Repo
**Recommended modern tool:**

```powershell
# Install via pip
pip install git-filter-repo

# Or download single file
# https://github.com/newren/git-filter-repo
```

### Task 11: Remove File with Filter-Repo
```powershell
# Fresh clone for clean demo
mkdir filter-repo-demo
cd filter-repo-demo
git init

# Create history
"secret" | Out-File secret.txt -Encoding UTF8
git add secret.txt
git commit -m "Add secret"

"work" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Work"

# Remove secret from history
git filter-repo --path secret.txt --invert-paths

# secret.txt is completely gone from history
git log --all -- secret.txt  # No results
```

### Task 12: Filter by Path
```powershell
# Keep only specific directory
git filter-repo --path src/ --path-rename src/:

# Remove specific directory
git filter-repo --path docs/ --invert-paths

# Keep multiple paths
git filter-repo --path app/ --path config/ --path-rename '':'backend/'
```

### Task 13: Rewrite Messages
```powershell
# Create file with replacements
@"
old_text==>new_text
bug==>fix
WIP==>work in progress
"@ | Out-File message-replacements.txt -Encoding UTF8

# Replace in all commit messages
git filter-repo --replace-message message-replacements.txt
```

### Task 14: Mailmap Corrections
Fix author information:

```powershell
# Create mailmap
@"
Correct Name <correct@email.com> <old@email.com>
Correct Name <correct@email.com> <another-old@email.com>
"@ | Out-File .mailmap -Encoding UTF8

# Apply mailmap
git filter-repo --mailmap .mailmap
```

## Part 5: Complete Cleanup Workflow

### Task 15: Full Repository Cleanup
Complete procedure for removing sensitive data:

```powershell
# ===== STEP 1: BACKUP =====
git clone --mirror <repo-url> repo-backup.git
# Keep this backup!

# ===== STEP 2: ANALYZE =====
# Find large files
git rev-list --objects --all | `
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | `
  Where-Object {$_ -match '^blob'} | `
  Sort-Object {[int]($_ -split '\s+')[2]} -Descending | `
  Select-Object -First 10

# Find file in history
git log --all --full-history -- path/to/file

# ===== STEP 3: REMOVE SENSITIVE DATA =====
# Option A: BFG (easiest)
bfg --delete-files sensitive.txt
bfg --replace-text passwords.txt

# Option B: Filter-repo (most powerful)
git filter-repo --path sensitive.txt --invert-paths

# Option C: Filter-branch (legacy)
git filter-branch --index-filter `
  "git rm --cached --ignore-unmatch sensitive.txt" `
  -- --all

# ===== STEP 4: VERIFY =====
# Search for sensitive data
git log --all -p | Select-String "SECRET_KEY"

# Check file doesn't exist in any commit
git log --all -- sensitive.txt

# ===== STEP 5: CLEANUP =====
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# ===== STEP 6: FORCE PUSH =====
# ⚠️ WARN YOUR TEAM FIRST!
git push origin --force --all
git push origin --force --tags

# ===== STEP 7: TEAM RE-CLONE =====
# Everyone on team must:
rm -rf local-repo
git clone <repo-url>

# Or rebase their work:
git fetch origin
git rebase origin/main
```

## ✅ Expected Outcome

You should understand:
- How to remove sensitive data from Git history
- Different tools for history rewriting
- When and when NOT to use these tools
- Force push implications
- Team coordination requirements

## 🎓 Key Concepts

### Tool Comparison

| Tool | Speed | Ease of Use | Power | Status |
|------|-------|-------------|-------|--------|
| BFG | ⚡⚡⚡ Fast | ✅ Easy | ⭐⭐ Good | Active |
| filter-repo | ⚡⚡ Fast | ⭐ Complex | ⭐⭐⭐ Best | Recommended |
| filter-branch | ⚡ Slow | ⭐ Complex | ⭐⭐ OK | Deprecated |

### When to Use Each Tool

**BFG**: Simple file/folder removal
**filter-repo**: Complex history rewriting
**filter-branch**: Legacy systems (avoid if possible)

## 🔍 Verification Commands

```powershell
# Search for sensitive data
git log --all -p | Select-String "SECRET"

# Find file in history
git log --all --full-history -- filename

# Check repo size
git count-objects -vH

# List all files ever in repo
git rev-list --objects --all | Select-String blob
```

## 💡 Pro Tips

### Before Cleanup
```powershell
# 1. Backup
git clone --mirror <url> backup.git

# 2. Notify team
# Post in Slack/Teams

# 3. Document what you're removing
echo "Removing .env, *.log, secrets/" > cleanup-log.txt
```

### After Cleanup
```powershell
# 1. Verify removal
# Search entire history

# 2. Test repository
# Clone fresh, run tests

# 3. Update documentation
# Note what was removed and why

# 4. Rotate secrets
# Changed exposed keys/passwords
```

### Prevent Future Issues
```powershell
# Add to .gitignore
echo .env >> .gitignore
echo *.log >> .gitignore
echo secrets/ >> .gitignore

# Use pre-commit hooks
# Install git-secrets or similar

# Use .env.example
# Template without real secrets
```

## 🎯 What You Should Know Now

- ✅ How to remove files from Git history
- ✅ Different history rewriting tools
- ✅ BFG vs filter-repo vs filter-branch
- ✅ How to remove sensitive data
- ✅ Force push implications
- ✅ Prevention strategies
- ✅ When NOT to rewrite history

## 📊 Cleanup Commands Cheatsheet

```powershell
# ===== BFG (Simplest) =====
bfg --delete-files filename
bfg --delete-folders foldername
bfg --replace-text passwords.txt
bfg --strip-blobs-bigger-than 100M

# ===== Filter-Repo (Powerful) =====
git filter-repo --path filename --invert-paths
git filter-repo --path-rename old/:new/
git filter-repo --replace-text replacements.txt
git filter-repo --mailmap .mailmap

# ===== Filter-Branch (Legacy) =====
git filter-branch --index-filter \
  "git rm --cached --ignore-unmatch file" \
  -- --all

# ===== Cleanup =====
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# ===== Force Push =====
git push --force --all
git push --force --tags
```

## 🚨 Common Scenarios

### Scenario 1: Committed API Key
```powershell
# 1. Remove immediately
bfg --replace-text 'sk_live_abc123==>***REMOVED***'

# 2. Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# 3. Force push
git push --force --all

# 4. ROTATE THE KEY!
# Generate new API key
```

### Scenario 2: Large File Bloat
```powershell
# Remove files over 10MB
bfg --strip-blobs-bigger-than 10M

# Or specific file
bfg --delete-files large-file.bin

# Clean up
git gc --prune=now --aggressive
```

### Scenario 3: Wrong Directory Structure
```powershell
# Restructure paths
git filter-repo --path-rename old/:new/
git filter-repo --path-rename '':'backend/'
```

## ⚠️ Critical Warnings

### Never Do This If:
- ❌ Repository is public and cloned by others
- ❌ Others have branches based on this history
- ❌ You haven't backed up
- ❌ You don't understand the consequences
- ❌ It's just to "clean up" old commits (use rebase instead)

### Always Do This:
- ✅ Backup first
- ✅ Coordinate with team
- ✅ Verify removal
- ✅ Rotate compromised secrets
- ✅ Test after cleanup
- ✅ Update .gitignore
- ✅ Document what was done

## 🎓 Post-Cleanup

### Team Instructions
After force push, everyone must:

```powershell
# Don't merge or rebase old work!
# Start fresh:
cd project
git fetch origin
git reset --hard origin/main

# Or fresh clone:
rm -rf project
git clone <url> project
```

## ⏭️ Congratulations!
You've completed all 20 Git exercises! You now have comprehensive Git knowledge from basics to advanced history manipulation.

---

**Time to Complete**: 50 minutes  
**Difficulty**: ⭐⭐⭐⭐ Expert  
**Prerequisites**: ALL previous exercises  
**⚠️ DANGER LEVEL**: Maximum - Use with extreme caution!

## 📚 Additional Resources

- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [git-filter-repo](https://github.com/newren/git-filter-repo)
- [GitHub: Removing Sensitive Data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [Git Documentation: filter-branch](https://git-scm.com/docs/git-filter-branch)
