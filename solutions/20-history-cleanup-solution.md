# Solution: Exercise 20 - History Cleanup

## ⚠️ EXTREME WARNING
**These commands rewrite history and can destroy data permanently!**  
**ALWAYS backup before proceeding!**

## Part 1: Understanding the Problem

```powershell
mkdir history-cleanup
cd history-cleanup
git init

# Accidentally commit sensitive data
@"
API_KEY=sk_live_secret_123456
DB_PASSWORD=super_secret_pass
AWS_KEY=AKIAIOSFODNN7EXAMPLE
"@ | Out-File .env -Encoding UTF8
git add .env
git commit -m "Add config"

# More commits
"Work" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Add app"

# Delete .env (but it's still in history!)
git rm .env
git commit -m "Remove .env"

# Still in history!
git log --all -- .env
# Still accessible!
```

## Part 2: Filter-Branch (Legacy)

```powershell
# Remove file from all history
git filter-branch --force --index-filter `
  "git rm --cached --ignore-unmatch .env" `
  --prune-empty --tag-name-filter cat -- --all

# Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Verify removal
git log --all -- .env  # No results!
```

## Part 3: BFG Repo-Cleaner (Recommended)

```powershell
# Install BFG
# Download from: https://rtyley.github.io/bfg-repo-cleaner/
# Or: scoop install bfg

# Clone as mirror for safety
git clone --mirror . ../backup.git

# Remove sensitive file
bfg --delete-files .env .

# Or remove pattern
bfg --delete-files '*.env' .
bfg --delete-files '*.log' .

# Remove large files
bfg --strip-blobs-bigger-than 100M .

# Replace sensitive strings
@"
SECRET_KEY_123
PASSWORD_456
API_KEY_789
"@ | Out-File passwords.txt -Encoding UTF8

bfg --replace-text passwords.txt .

# Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

## Part 4: Git Filter-Repo (Modern)

```powershell
# Install filter-repo
# pip install git-filter-repo

# Remove file
git filter-repo --path .env --invert-paths

# Remove directory
git filter-repo --path secrets/ --invert-paths

# Keep only specific paths
git filter-repo --path src/ --path config/

# Rewrite commit messages
@"
old_text==>new_text
bug==>fix
WIP==>work in progress
"@ | Out-File message-replacements.txt -Encoding UTF8

git filter-repo --replace-message message-replacements.txt

# Fix author information
@"
Correct Name <correct@email.com> <old@email.com>
"@ | Out-File .mailmap -Encoding UTF8

git filter-repo --mailmap .mailmap
```

## Part 5: Complete Cleanup Workflow

```powershell
# ===== STEP 1: BACKUP =====
git clone --mirror . ../repo-backup.git
# KEEP THIS BACKUP!

# ===== STEP 2: ANALYZE =====
# Find large files
git rev-list --objects --all | `
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | `
  Where-Object {$_ -match '^blob'} | `
  Sort-Object {[int]($_ -split '\s+')[2]} -Descending | `
  Select-Object -First 10

# Find sensitive file in history
git log --all --full-history -- .env

# ===== STEP 3: REMOVE DATA =====
# Option A: BFG (easiest)
bfg --delete-files .env
bfg --replace-text passwords.txt

# Option B: Filter-repo (powerful)
git filter-repo --path .env --invert-paths

# ===== STEP 4: VERIFY =====
# Search for sensitive data
git log --all -p | Select-String "SECRET_KEY"

# Check file doesn't exist
git log --all -- .env

# ===== STEP 5: CLEANUP =====
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# ===== STEP 6: FORCE PUSH =====
# ⚠️ WARN YOUR TEAM FIRST!
# git push origin --force --all
# git push origin --force --tags

# ===== STEP 7: TEAM RE-CLONE =====
# Everyone must:
# rm -rf local-repo
# git clone <repo-url>
```

## Common Scenarios

### Scenario 1: Committed API Key
```powershell
# Remove immediately
bfg --replace-text 'sk_live_abc123==>***REMOVED***'
git reflog expire --expire=now --all
git gc --prune=now --aggressive
# git push --force --all

# ROTATE THE KEY IMMEDIATELY!
```

### Scenario 2: Large File Bloat
```powershell
# Remove large files
bfg --strip-blobs-bigger-than 10M

# Or specific file
bfg --delete-files large-video.mp4

# Clean up
git gc --prune=now --aggressive
```

### Scenario 3: Restructure Paths
```powershell
# Move everything to subdirectory
git filter-repo --path-rename '':'backend/'

# Rename directory
git filter-repo --path-rename 'old/':'new/'
```

## Tool Comparison

| Tool | Speed | Ease | Power | Status |
|------|-------|------|-------|--------|
| BFG | ⚡⚡⚡ | ✅ Easy | ⭐⭐ | Active |
| filter-repo | ⚡⚡ | ⭐ Complex | ⭐⭐⭐ | Recommended |
| filter-branch | ⚡ | ⭐ Complex | ⭐⭐ | Deprecated |

## Important Commands

```powershell
# ===== BFG =====
bfg --delete-files <filename>
bfg --delete-folders <folder>
bfg --replace-text <file>
bfg --strip-blobs-bigger-than <size>

# ===== Filter-Repo =====
git filter-repo --path <path> --invert-paths
git filter-repo --path-rename <old>:<new>
git filter-repo --replace-text <replacements>
git filter-repo --mailmap <mailmap>

# ===== Cleanup =====
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# ===== Force Push =====
git push --force --all
git push --force --tags
```

## When to Use

### ✅ Use When
- Sensitive data committed (passwords, keys)
- Large files accidentally committed
- Need to restructure history
- Fixing author information

### ❌ NEVER Use When
- Just to "clean up" history
- On public branches others use
- Without team coordination
- Without backups
- Don't understand consequences

## Prevention

```powershell
# Add to .gitignore
echo .env >> .gitignore
echo *.log >> .gitignore
echo secrets/ >> .gitignore
git add .gitignore
git commit -m "Add gitignore"

# Use pre-commit hooks
# Install git-secrets
# https://github.com/awslabs/git-secrets

# Create .env.example
@"
API_KEY=your_key_here
DB_PASSWORD=your_password_here
"@ | Out-File .env.example -Encoding UTF8
```

## Verification

✅ You should now understand:
- How to remove files from history
- BFG vs filter-repo vs filter-branch
- Complete cleanup workflow
- Force push implications
- Prevention strategies
- When NOT to rewrite history

## Pro Tips

### Backup First
```powershell
git clone --mirror . ../backup.git
```

### Verify Before Push
```powershell
# Search entire history
git log --all -p | Select-String "SECRET"
```

### Team Communication
```powershell
# Before force push:
# 1. Notify team in Slack/Teams
# 2. Document what's being removed
# 3. Provide instructions for re-cloning
# 4. Set a time for the operation
```

### After Cleanup
```powershell
# 1. Verify removal
# 2. Test repository
# 3. Rotate compromised secrets
# 4. Update documentation
# 5. Team re-clones
```

## Critical Warnings

### Never Do This If
- ❌ Repository is public
- ❌ Others have based work on history
- ❌ You haven't backed up
- ❌ You don't understand consequences
- ❌ Just for "cleanup"

### Always Do This
- ✅ Backup first
- ✅ Coordinate with team
- ✅ Verify removal
- ✅ Rotate secrets
- ✅ Test after cleanup
- ✅ Update .gitignore
- ✅ Document changes

---

**Exercise completed!** ✅  
**You now understand history rewriting - use with EXTREME caution!**

## Post-Cleanup Team Instructions

After force push, everyone must:
```powershell
# Don't merge old work!
cd project
git fetch origin
git reset --hard origin/main

# Or fresh clone:
rm -rf project
git clone <url> project
```

**Congratulations on completing all 20 exercises!** 🎉
