# Solution: Exercise 11 - Stashing

## Complete Command Sequence

```powershell
# Preparation
mkdir stash-practice
cd stash-practice
git init
"Initial" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "Initial commit"

# Task 1: Basic stash
"Work in progress" | Out-File file.txt -Encoding UTF8
git status  # Modified
git stash
git status  # Clean!
Get-Content file.txt  # Back to "Initial"

# Task 2: View stash list
git stash list
# stash@{0}: WIP on main: abc1234 Initial commit

# Task 3: Apply stash
git stash apply
Get-Content file.txt  # "Work in progress" is back
git stash list  # Stash still in list

# Task 4: Pop stash
git stash
git stash pop
# Applies and removes from list
git stash list  # Empty

# Task 5: Stash with message
"New work" | Out-File file.txt -Encoding UTF8
git stash save "Working on new feature"
git stash list
# stash@{0}: On main: Working on new feature

# Task 6: Stash untracked files
"Untracked" | Out-File new.txt -Encoding UTF8
git status  # new.txt untracked
git stash -u
# Or: git stash --include-untracked
git status  # Clean, new.txt stashed

# Task 7: Stash all files (including ignored)
".env content" | Out-File .env -Encoding UTF8
"*.env" | Out-File .gitignore -Encoding UTF8
git add .gitignore
git commit -m "Add gitignore"
".env changes" | Out-File .env -Encoding UTF8
git stash -a
# Or: git stash --all

# Task 8: Multiple stashes
"Change 1" | Out-File file.txt -Encoding UTF8
git stash save "Change 1"
"Change 2" | Out-File file.txt -Encoding UTF8
git stash save "Change 2"
"Change 3" | Out-File file.txt -Encoding UTF8
git stash save "Change 3"
git stash list
# stash@{0}: Change 3
# stash@{1}: Change 2
# stash@{2}: Change 1

# Task 9: Apply specific stash
git stash apply stash@{1}
# Applies "Change 2"

# Task 10: View stash contents
git stash show
# Shows file statistics
git stash show -p
# Shows full diff
git stash show stash@{1} -p
# Shows specific stash

# Task 11: Create branch from stash
git stash
git stash branch new-feature
# Creates branch and applies stash
# Useful if stash conflicts with current branch

# Task 12: Partial stash
"Line 1" | Out-File multi.txt -Encoding UTF8
git add multi.txt
git commit -m "Add multi"
"Line 2`nLine 3" | Add-Content multi.txt
git stash -p
# Interactive: choose what to stash
# y = stash this hunk
# n = don't stash

# Task 13: Drop specific stash
git stash list
git stash drop stash@{1}
git stash list  # stash@{1} removed

# Task 14: Clear all stashes
git stash clear
git stash list  # Empty

# Task 15: Stash workflow
# Working on feature
git checkout -b feature
"Feature work" | Out-File feature.txt -Encoding UTF8
git add feature.txt

# Need to switch to hotfix!
git stash save "WIP: feature work"

# Fix bug
git checkout main
git checkout -b hotfix
"Hotfix" | Out-File fix.txt -Encoding UTF8
git add fix.txt
git commit -m "Hotfix"
git checkout main
git merge hotfix

# Back to feature
git checkout feature
git stash pop
# Continue working
```

## Stash Commands

### Basic Operations
```powershell
git stash                   # Stash changes
git stash save "message"    # Stash with message
git stash list              # View stashes
git stash apply             # Apply latest stash (keeps in list)
git stash pop               # Apply and remove from list
git stash drop              # Remove latest stash
git stash drop stash@{1}    # Remove specific stash
git stash clear             # Remove all stashes
```

### Advanced Options
```powershell
git stash -u                # Include untracked files
git stash -a                # Include all (even ignored)
git stash -p                # Interactive/partial stash
git stash -k                # Keep staged changes
git stash --include-untracked  # Same as -u
git stash --all             # Same as -a
git stash --patch           # Same as -p
```

### Viewing Stashes
```powershell
git stash show              # Show stats
git stash show -p           # Show diff
git stash show stash@{1}    # Show specific stash
git stash list              # List all stashes
```

### Applying Stashes
```powershell
git stash apply             # Apply latest
git stash apply stash@{1}   # Apply specific
git stash pop               # Apply and remove
git stash branch <name>     # Create branch from stash
```

## Stash Stack

Stashes work like a stack (Last In, First Out):

```
git stash save "Work 1"     →  stash@{0}: Work 1
git stash save "Work 2"     →  stash@{0}: Work 2
                                stash@{1}: Work 1
git stash save "Work 3"     →  stash@{0}: Work 3
                                stash@{1}: Work 2
                                stash@{2}: Work 1

git stash pop               →  stash@{0}: Work 2
                                stash@{1}: Work 1
```

## Common Workflows

### Quick Context Switch
```powershell
# Working on feature
git stash

# Switch context
git checkout main
# ... do urgent work ...

# Return to feature
git checkout feature
git stash pop
```

### Stash Before Pull
```powershell
# Have uncommitted changes
git stash
git pull
git stash pop
# Merge local changes with pulled changes
```

### Try Changes on Different Branch
```powershell
# Changes on wrong branch
git stash
git checkout correct-branch
git stash pop
```

### Clean Workspace Temporarily
```powershell
# Need clean workspace to test
git stash
# ... test clean state ...
git stash pop
```

## Important Notes

### What Gets Stashed
- ✅ Modified tracked files
- ✅ Staged changes
- ⚠️ Untracked files (only with `-u`)
- ⚠️ Ignored files (only with `-a`)

### Stash vs Commit
**Use Stash when:**
- Temporary work
- Not ready to commit
- Need to switch context quickly
- Experimental changes

**Use Commit when:**
- Logical unit of work complete
- Want to share with team
- Want permanent history
- Ready for review

## Verification

✅ You should now understand:
- Creating stashes
- Applying and popping stashes
- Multiple stashes
- Stashing untracked files
- Viewing stash contents
- Dropping stashes
- Creating branches from stashes

## Pro Tips

### Name Your Stashes
```powershell
git stash save "WIP: implementing login feature"
git stash save "Experiment: trying new approach"
git stash save "Bugfix: needs more testing"
```

### View Before Applying
```powershell
git stash show -p stash@{1}
# Check what's in stash before applying
```

### Stash Individual Files
```powershell
git stash -p
# Choose which hunks to stash interactively
```

### Recover Dropped Stash
```powershell
git fsck --unreachable | grep commit
git show <commit-hash>
git stash apply <commit-hash>
```

### Keep Index
```powershell
git stash -k
# Stashes changes but keeps staged files staged
```

---

**Exercise completed!** ✅  
You now master stashing for context switching!
