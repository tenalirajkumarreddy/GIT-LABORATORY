# Solution: Exercise 10 - Undoing Changes

## Complete Command Sequence

```powershell
# Preparation
mkdir undo-practice
cd undo-practice
git init
"Initial" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "Initial commit"

# Task 1: Undo unstaged changes
"Unwanted changes" | Out-File file.txt -Encoding UTF8
git status  # Modified
git restore file.txt
Get-Content file.txt  # Back to "Initial"

# Task 2: Unstage files
"Change" | Out-File file.txt -Encoding UTF8
git add file.txt
git status  # Staged
git restore --staged file.txt
git status  # Unstaged

# Task 3: Discard all unstaged changes
"Change 1" | Out-File file1.txt -Encoding UTF8
"Change 2" | Out-File file2.txt -Encoding UTF8
git restore .
# All changes discarded

# Task 4: Amend last commit
"Content" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "Add content"
"Forgot this" | Out-File forgot.txt -Encoding UTF8
git add forgot.txt
git commit --amend --no-edit
git log --oneline  # Only one commit

# Task 5: Amend commit message
git commit --amend -m "Better commit message"
git log --oneline  # Message changed

# Task 6: Soft reset
"A" | Out-File a.txt -Encoding UTF8
git add a.txt
git commit -m "Commit A"
"B" | Out-File b.txt -Encoding UTF8
git add b.txt
git commit -m "Commit B"
git reset --soft HEAD~1
git status  # Changes still staged
git log --oneline  # Commit B gone

# Task 7: Mixed reset (default)
git commit -m "Commit B again"
git reset HEAD~1
git status  # Changes unstaged
git log --oneline  # Commit B gone

# Task 8: Hard reset
git add .
git commit -m "Commit B"
git reset --hard HEAD~1
git status  # Clean working directory
# All changes GONE!

# Task 9: Reset to specific commit
git log --oneline
git reset --hard <commit-hash>
# Resets to that commit

# Task 10: Revert commit
"Feature" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "Add feature"
git revert HEAD
# Creates new commit undoing the change
git log --oneline  # Shows both commits

# Task 11: Revert multiple commits
"C1" | Out-File c1.txt -Encoding UTF8
git add c1.txt
git commit -m "C1"
"C2" | Out-File c2.txt -Encoding UTF8
git add c2.txt
git commit -m "C2"
git revert HEAD~1..HEAD
# Reverts both commits

# Task 12: Revert without commit
git revert -n HEAD
git status  # Changes staged but not committed
git commit -m "Revert feature"

# Task 13: Recover from reset
git reflog
git reset --hard HEAD@{1}
# Undoes the reset!

# Task 14: Clean untracked files
"Temp" | Out-File temp.txt -Encoding UTF8
"Junk" | Out-File junk.txt -Encoding UTF8
git clean -n  # Dry run
git clean -f  # Remove untracked files
git clean -fd # Remove files and directories

# Task 15: Undo published commit
"Public change" | Out-File public.txt -Encoding UTF8
git add public.txt
git commit -m "Public commit"
# Assume this was pushed
git revert HEAD
# Safe way - creates new commit
git push
```

## Reset Types Comparison

### Soft Reset
```powershell
git reset --soft HEAD~1
```
- Moves HEAD
- Keeps staging area
- Keeps working directory
- **Use for:** Re-commit with changes

### Mixed Reset (default)
```powershell
git reset HEAD~1
# Same as: git reset --mixed HEAD~1
```
- Moves HEAD
- Clears staging area
- Keeps working directory
- **Use for:** Unstage and re-stage

### Hard Reset
```powershell
git reset --hard HEAD~1
```
- Moves HEAD
- Clears staging area
- **Clears working directory** ⚠️
- **Use for:** Complete undo (dangerous!)

## Comparison Table

| Command | Working Dir | Staging | History | Safe? |
|---------|-------------|---------|---------|-------|
| `restore` | Changes | No change | No change | ✅ Yes |
| `restore --staged` | No change | Clears | No change | ✅ Yes |
| `reset --soft` | No change | No change | Moves HEAD | ⚠️ Careful |
| `reset --mixed` | No change | Clears | Moves HEAD | ⚠️ Careful |
| `reset --hard` | **Clears** | Clears | Moves HEAD | ❌ Dangerous |
| `revert` | Changes | Stages | Adds commit | ✅ Yes |
| `clean` | Removes untracked | No change | No change | ⚠️ Careful |

## Important Commands

```powershell
# Undo changes
git restore <file>              # Discard unstaged changes
git restore --staged <file>     # Unstage
git restore .                   # Discard all changes

# Amend
git commit --amend              # Change last commit
git commit --amend -m "New msg" # Change message
git commit --amend --no-edit    # Add to last commit

# Reset
git reset --soft HEAD~1         # Undo commit, keep changes staged
git reset HEAD~1                # Undo commit, unstage changes
git reset --hard HEAD~1         # Undo commit, delete changes
git reset --hard <commit>       # Reset to specific commit

# Revert
git revert HEAD                 # Create new commit undoing last
git revert <commit>             # Revert specific commit
git revert -n HEAD              # Revert without committing

# Clean
git clean -n                    # Dry run
git clean -f                    # Remove untracked files
git clean -fd                   # Remove files and directories
git clean -fX                   # Remove only ignored files

# Recover
git reflog                      # View history
git reset --hard HEAD@{1}       # Undo reset
```

## When to Use What

### Undo Unstaged Changes
```powershell
git restore <file>
```

### Unstage Files
```powershell
git restore --staged <file>
```

### Fix Last Commit
```powershell
git commit --amend
```

### Undo Local Commits (Not Pushed)
```powershell
git reset HEAD~1
```

### Undo Public Commits (Already Pushed)
```powershell
git revert HEAD
```

### Remove Untracked Files
```powershell
git clean -f
```

## Common Scenarios

### Scenario 1: Made Mistake in Last Commit
```powershell
# Add forgotten file
git add forgotten.txt
git commit --amend --no-edit
```

### Scenario 2: Want to Split Last Commit
```powershell
git reset HEAD~1
git add file1.txt
git commit -m "First part"
git add file2.txt
git commit -m "Second part"
```

### Scenario 3: Committed to Wrong Branch
```powershell
# On main by mistake
git log --oneline -1  # Note commit hash
git reset --hard HEAD~1  # Remove from main
git checkout correct-branch
git cherry-pick <hash>  # Add to correct branch
```

### Scenario 4: Need to Undo Public Commit
```powershell
# Already pushed, can't use reset!
git revert HEAD
git push
```

## Verification

✅ You should now understand:
- Discarding unstaged changes
- Unstaging files
- Amending commits
- Reset types (soft, mixed, hard)
- Reverting commits
- Cleaning untracked files
- When to use each method

## Pro Tips

### Safe Practices
```powershell
# Before dangerous operation
git branch backup
git stash save "Before reset"

# Then do operation
git reset --hard HEAD~5

# If needed:
git reset --hard backup
```

### View Before Reset
```powershell
# See what will be lost
git log HEAD~3..HEAD
git diff HEAD~3

# Then reset
git reset --hard HEAD~3
```

### Undo Accidental Reset
```powershell
git reflog
git reset --hard HEAD@{1}
```

---

**Exercise completed!** ✅  
You can now undo changes safely!
