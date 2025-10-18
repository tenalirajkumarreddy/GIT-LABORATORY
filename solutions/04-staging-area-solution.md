# Solution: Exercise 04 - Staging Area

## Complete Command Sequence

```powershell
# Preparation
mkdir staging-practice
cd staging-practice
git init

# Task 1: Stage single file
"File 1" | Out-File file1.txt -Encoding UTF8
git add file1.txt
git status  # Shows file1.txt staged

# Task 2: Stage multiple files
"File 2" | Out-File file2.txt -Encoding UTF8
"File 3" | Out-File file3.txt -Encoding UTF8
git add file2.txt file3.txt
git status  # Both files staged

# Task 3: Stage all files
"File 4" | Out-File file4.txt -Encoding UTF8
"File 5" | Out-File file5.txt -Encoding UTF8
git add .
git status  # All files staged

# Task 4: Commit staged changes
git commit -m "Initial commit with 5 files"
git status  # Clean working directory

# Task 5: Selective staging
"Line 1" | Out-File code.txt -Encoding UTF8
"Line 2" | Add-Content code.txt
"Line 3" | Add-Content code.txt
git add code.txt
git commit -m "Add code file"

"Line 4" | Add-Content code.txt
"Line 5" | Add-Content code.txt
git add -p code.txt
# Choose 'y' to stage hunks

# Task 6: View staged changes
git diff --staged
# Shows what will be committed

# Task 7: Unstage files
"New file" | Out-File unstage-me.txt -Encoding UTF8
git add unstage-me.txt
git restore --staged unstage-me.txt
git status  # File is untracked again

# Task 8: Stage partial changes
"Bug fix" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Add app"

"Feature 1`nDebug code`nFeature 2" | Out-File app.js -Encoding UTF8
git add -p app.js
# Stage only features, not debug

# Task 9: Interactive staging
git add -i
# Choose options:
# 2 (update) to stage files
# 3 (revert) to unstage
# 4 (patch) for partial staging

# Task 10: Stage deleted files
git rm file1.txt
git status  # Deletion is staged

# Task 11: Stage renamed files
git mv file2.txt file2-renamed.txt
git status  # Shows as renamed

# Task 12: Atomic commits
"Feature A part 1" | Out-File featureA.txt -Encoding UTF8
"Feature B part 1" | Out-File featureB.txt -Encoding UTF8
git add featureA.txt
git commit -m "feat: implement feature A"
git add featureB.txt
git commit -m "feat: implement feature B"

# Task 13: Reset staged changes
"Changes" | Out-File test.txt -Encoding UTF8
git add test.txt
git reset HEAD test.txt  # Unstage
git status  # File is modified, not staged

# Task 14: Stage by pattern
"Config 1" | Out-File config1.json -Encoding UTF8
"Config 2" | Out-File config2.json -Encoding UTF8
"Config 3" | Out-File config3.json -Encoding UTF8
git add *.json
git status  # All JSON files staged

# Task 15: Check what's staged
git status
git diff --staged
git diff --name-only --staged
```

## Key Concepts

### Staging Area Purpose
- **Buffer between working directory and commits**
- **Allows selective commits**
- **Enables atomic commits**
- **Review before commit**

### Important Commands
```powershell
git add <file>              # Stage file
git add .                   # Stage all
git add -p                  # Interactive staging
git add -i                  # Interactive mode
git restore --staged <file> # Unstage
git diff --staged           # View staged changes
git reset HEAD <file>       # Unstage (old way)
```

## Common Workflows

### Selective Staging
```powershell
# Made changes to multiple files
git add file1.txt      # Stage file1
git commit -m "Fix 1"  # Commit just file1
git add file2.txt      # Stage file2
git commit -m "Fix 2"  # Commit just file2
```

### Partial Staging (-p flag)
```powershell
# Made multiple changes in one file
git add -p file.txt
# Answer prompts:
# y = yes, stage this hunk
# n = no, don't stage
# s = split into smaller hunks
# q = quit
```

### Interactive Mode
```powershell
git add -i
# Menu:
# 1: status
# 2: update (stage files)
# 3: revert (unstage)
# 4: add untracked
# 5: patch (partial staging)
# 7: quit
```

## Verification

✅ You should now understand:
- How to stage files selectively
- Difference between working directory and staging area
- Partial staging with -p
- Interactive staging with -i
- How to unstage files
- Atomic commits

## Pro Tips

### Check Before Commit
```powershell
git status        # What's staged?
git diff --staged # Exactly what will be committed
git commit        # Now commit
```

### Unstage Everything
```powershell
git restore --staged .
# Or (old way):
git reset HEAD .
```

### Stage Deleted Files
```powershell
# File was deleted
git add deleted-file.txt  # Stages deletion
# Or:
git rm deleted-file.txt   # Delete and stage
```

---

**Exercise completed!** ✅  
You now understand the staging area and selective commits!
