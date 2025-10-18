# Solution: Exercise 17 - Reflog Recovery

## Complete Command Sequence

```powershell
# Preparation
mkdir reflog-recovery
cd reflog-recovery
git init
"version 1" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "v1"
"version 2" | Out-File app.js -Encoding UTF8
git commit -am "v2"
"version 3" | Out-File app.js -Encoding UTF8
git commit -am "v3"

# Task 1: Understanding reflog
git reflog
# Shows every HEAD change

# Task 2: Reflog shows everything
git checkout HEAD~1
git checkout main
git checkout -b feature
git checkout main
git reflog
# All operations tracked!

# Task 3: Recover from hard reset
git log --oneline
# abc1234 v3
# def5678 v2
# ghi9012 v1

git reset --hard HEAD~2
git log --oneline
# Only v1 shows!

git reflog
# Find v3 commit
git reset --hard HEAD@{1}
# v3 is back!

# Task 4: Restore deleted branch
git checkout -b important-feature
"important work" | Out-File feature.js -Encoding UTF8
git add feature.js
git commit -m "important feature"
git log --oneline -1  # Note hash: xyz9876

git checkout main
git branch -D important-feature
git branch  # Branch gone!

git reflog
# Find: xyz9876 HEAD@{3}: commit: important feature
git branch important-feature xyz9876
# Branch restored!

# Task 5: Undo bad rebase
git checkout -b feature
"feat1" | Out-File feat.js -Encoding UTF8
git add feat.js
git commit -m "feat1"
git checkout main
"conflict" | Out-File feat.js -Encoding UTF8
git add feat.js
git commit -m "main work"
git checkout feature
git rebase main  # Messy!
git rebase --abort

# Or if completed badly:
git reflog
git reset --hard HEAD@{5}  # Before rebase

# Task 6: Recover lost commits
"commit 1" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "commit 1"
"commit 2" | Out-File file.txt -Encoding UTF8
git commit -am "commit 2"
git log --oneline
# aaa1111 commit 2
# bbb2222 commit 1

git reset --hard HEAD~2
git log --oneline  # Commits gone!

git reflog
# Find commits
git cherry-pick bbb2222
git cherry-pick aaa1111
# Or: git reset --hard HEAD@{2}

# Task 7: Time travel with HEAD@{n}
git log HEAD@{5} -1    # 5 moves ago
git show HEAD@{10}     # 10 moves ago
git checkout HEAD@{5}  # Checkout old state
git checkout main

# Task 8: Reflog with time
git show HEAD@{1.hour.ago}
git show HEAD@{yesterday}
git show HEAD@{2.weeks.ago}
git show 'HEAD@{2025-10-18 14:30:00}'

# Task 9: Reflog for specific branch
git reflog show main
git reflog show feature
git reflog show HEAD

# Task 10: Recover stashed changes
"stashed work" | Out-File stash.txt -Encoding UTF8
git add stash.txt
git stash
git stash list  # stash@{0}

git stash drop stash@{0}
git stash list  # Empty!

git fsck --lost-found
# Or: git reflog show stash
git stash apply <hash>

# Task 11: Find when bug introduced
git reflog
# Test different states
git checkout HEAD@{5}  # Test... works
git checkout HEAD@{3}  # Test... broken
# Bug between HEAD@{5} and HEAD@{3}

# Task 12: Clean up reflog
git config gc.reflogExpire  # Shows 90 days
git reflog expire --expire=now --all
git gc --prune=now
# ⚠️ Makes recovery impossible!

# Task 13: Recover from pull mistake
git log --oneline -3
# abc1 commit 3
# abc2 commit 2
git pull origin main  # Messy merge!

git reflog
# Find before pull
git reset --hard HEAD@{1}
git pull --rebase origin main  # Better!

# Task 14: Recover from detached HEAD
git checkout HEAD~3
"new feature" | Out-File new.js -Encoding UTF8
git add new.js
git commit -m "new feature"  # xyz1234
git log --oneline -1  # Note hash

git checkout main  # Lost!
git reflog
# Find: xyz1234 HEAD@{2}: commit
git branch recovered-feature xyz1234
# Or: git cherry-pick xyz1234

# Task 15: Complete disaster recovery
git checkout -b important-work
"Critical" | Out-File critical.js -Encoding UTF8
git add critical.js
git commit -m "Critical"
"More" | Add-Content critical.js
git commit -am "More"

git checkout main
git branch -D important-work
git reset --hard HEAD~5

# PANIC! Everything gone!

git reflog
# Find commits:
# abc1234 HEAD@{2}: commit: More
# def5678 HEAD@{3}: commit: Critical

git checkout -b recovered abc1234
git log --oneline  # All back!
```

## Reflog Concepts

### What is Reflog?
- Records every change to HEAD
- Local to your repository
- Expires after 90 days (default)
- Your safety net for mistakes

### HEAD@{n} Syntax
```
HEAD@{0}  = Current HEAD
HEAD@{1}  = One move ago
HEAD@{2}  = Two moves ago
...
```

## Important Commands

```powershell
# View reflog
git reflog
git reflog show HEAD
git reflog show main

# With dates
git reflog --date=relative
git reflog --date=iso

# HEAD references
HEAD@{0}                  # Current
HEAD@{1}                  # One ago
HEAD@{5}                  # Five ago
HEAD@{yesterday}          # Yesterday
HEAD@{1.hour.ago}         # Hour ago
HEAD@{2.weeks.ago}        # 2 weeks ago
HEAD@{2025-10-18}         # Specific date

# Recovery
git reset --hard HEAD@{1}         # Undo operation
git branch name HEAD@{3}          # Recover branch
git cherry-pick <hash>            # Recover commit
git checkout -b new-branch <hash> # Branch from lost commit

# Cleanup (dangerous!)
git reflog expire --expire=now --all
git gc --prune=now
```

## Common Recovery Scenarios

### Accidental Hard Reset
```powershell
git reset --hard HEAD~5  # Oops!
git reflog
git reset --hard HEAD@{1}  # Fixed!
```

### Deleted Branch
```powershell
git branch -D feature  # Oops!
git reflog
git branch feature <hash>  # Fixed!
```

### Lost Commits
```powershell
git reflog
git cherry-pick <hash>  # Recover
```

### Bad Merge
```powershell
git merge feature  # Messy!
git reflog
git reset --hard HEAD@{1}  # Undo
```

## Verification

✅ You should now understand:
- What reflog is and how it works
- Recovering from hard resets
- Restoring deleted branches
- Finding lost commits
- Time-based HEAD references
- When reflog can't help

## Pro Tips

### Check Before Dangerous Operation
```powershell
git rev-parse HEAD  # Note current
# Or:
git branch backup
```

### Reflog Aliases
```powershell
git config --global alias.undo 'reset --hard HEAD@{1}'
git config --global alias.whoops 'reflog'
```

### Search Reflog
```powershell
git reflog | Select-String "important"
git reflog | Select-String "checkout"
git reflog | Select-String "reset"
```

### View Reflog with Graph
```powershell
git log -g --oneline --graph
```

## When Reflog Can't Help

Reflog can't recover:
- ❌ Untracked files (never added)
- ❌ Changes never committed
- ❌ After `git gc --prune=now`
- ❌ After 90 days (expired)
- ❌ In other repos (reflog is local)

## Reflog vs Log

| `git log` | `git reflog` |
|-----------|--------------|
| Commit history | HEAD movement history |
| Follows branches | Shows all ref changes |
| Public history | Local only |
| Doesn't show lost | Shows "lost" commits |

---

**Exercise completed!** ✅  
Reflog is your Git time machine - nothing is truly lost!
