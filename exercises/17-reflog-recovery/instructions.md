# Exercise 17: Reflog Recovery - Your Git Time Machine

## 🎯 Objective
Master Git's reflog to recover "lost" commits, undo mistakes, and restore deleted branches.

## 📚 Concepts Covered
- Git reflog (reference log)
- HEAD history
- Recovering lost commits
- Undoing resets
- Restoring deleted branches
- Time-travel with HEAD@{n}
- Garbage collection

## 🔑 Key Insight
**In Git, nothing is truly lost (for ~90 days)!**  
Reflog tracks every change to HEAD, allowing you to recover almost anything.

## 📝 Preparation

```powershell
mkdir reflog-recovery
cd reflog-recovery
git init

# Create some history
"version 1" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "v1"

"version 2" | Out-File app.js -Encoding UTF8
git commit -am "v2"

"version 3" | Out-File app.js -Encoding UTF8
git commit -am "v3"
```

## 📝 Tasks

### Task 1: Understanding Reflog
1. View reflog: `git reflog`
   ```
   abc1234 (HEAD -> main) HEAD@{0}: commit: v3
   def5678 HEAD@{1}: commit: v2
   ghi9012 HEAD@{2}: commit (initial): v1
   ```
2. Shows every change to HEAD
3. Each entry has a reference like `HEAD@{0}` (most recent)

### Task 2: Reflog Shows Everything
Make various changes and see reflog track them:

```powershell
# Checkout different commits
git checkout HEAD~1
git checkout main

# Create and switch branches
git checkout -b feature
git checkout main

# View reflog
git reflog

# See all operations tracked:
# - commits
# - checkouts
# - branch switches
# - merges
# - resets
# - etc.
```

### Task 3: Recover from Hard Reset
Simulate disaster and recover:

```powershell
# Note current commit
git log --oneline
# abc1234 v3
# def5678 v2
# ghi9012 v1

# DISASTER! Hard reset to v1
git reset --hard HEAD~2

# Check log - v2 and v3 are "gone"!
git log --oneline
# ghi9012 v1

# But reflog remembers!
git reflog
# ghi9012 (HEAD -> main) HEAD@{0}: reset: moving to HEAD~2
# abc1234 HEAD@{1}: commit: v3
# ...

# RECOVER!
git reset --hard HEAD@{1}

# Or use the commit hash
git reset --hard abc1234

# Check log - v3 is back!
git log --oneline
```

### Task 4: Restore Deleted Branch
Recover a deleted branch:

```powershell
# Create feature branch
git checkout -b important-feature
"important work" | Out-File feature.js -Encoding UTF8
git add feature.js
git commit -m "important feature"

# Note the commit hash
git log --oneline -1
# xyz9876 important feature

# Go back to main
git checkout main

# OOPS! Delete the branch
git branch -D important-feature

# Branch is gone!
git branch
# * main

# But reflog remembers!
git reflog
# ... HEAD@{2}: checkout: moving from important-feature to main
# xyz9876 HEAD@{3}: commit: important feature

# RECOVER! Recreate branch at that commit
git branch important-feature HEAD@{3}
# Or: git branch important-feature xyz9876

# Branch is back!
git branch
```

### Task 5: Undo Bad Rebase
Recover from rebase gone wrong:

```powershell
# Create messy situation
git checkout -b feature
"feat1" | Out-File feat.js -Encoding UTF8
git add feat.js
git commit -m "feat1"

"feat2" | Out-File feat.js -Encoding UTF8
git commit -am "feat2"

# Rebase with conflicts
git checkout main
"conflict" | Out-File feat.js -Encoding UTF8
git add feat.js
git commit -m "main work"

git checkout feature
git rebase main

# Conflicts! You panic and abort
git rebase --abort

# Or worse, you mess it up completely
# Want to go back to before rebase?

git reflog
# Find "rebase finished" or "rebase started"
# Go back to before rebase
git reset --hard HEAD@{5}  # Adjust number
```

### Task 6: Recover Lost Commits
Recover commits that seem lost:

```powershell
git checkout main
"commit 1" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "commit 1"

"commit 2" | Out-File file.txt -Encoding UTF8
git commit -am "commit 2"

"commit 3" | Out-File file.txt -Encoding UTF8
git commit -am "commit 3"

# View commits
git log --oneline
# aaa1111 commit 3
# bbb2222 commit 2
# ccc3333 commit 1

# Reset back, losing commits
git reset --hard HEAD~3

# Commits seem gone
git log --oneline
# (only shows old history)

# But reflog has them!
git reflog
# ccc3333 HEAD@{1}: reset: moving to HEAD~3
# aaa1111 HEAD@{2}: commit: commit 3
# bbb2222 HEAD@{3}: commit: commit 2

# Recover
git cherry-pick bbb2222
git cherry-pick aaa1111

# Or reset back
git reset --hard HEAD@{2}
```

### Task 7: Time Travel with HEAD@{n}
Navigate through history:

```powershell
# Where was HEAD 5 moves ago?
git log HEAD@{5} -1

# What was the state 10 moves ago?
git show HEAD@{10}

# Checkout old state
git checkout HEAD@{5}

# See diff between now and 3 moves ago
git diff HEAD@{3} HEAD
```

### Task 8: Reflog with Time
Use time-based references:

```powershell
# Where was HEAD 1 hour ago?
git show HEAD@{1.hour.ago}

# Yesterday
git show HEAD@{yesterday}

# 2 weeks ago
git show HEAD@{2.weeks.ago}

# Specific date/time
git show HEAD@{2025-10-18}
git show 'HEAD@{2025-10-18 14:30:00}'
```

### Task 9: Reflog for Specific Branch
Each branch has its own reflog:

```powershell
# View reflog for main branch
git reflog show main

# View reflog for feature branch
git reflog show feature

# View reflog for specific ref
git reflog show HEAD
```

### Task 10: Recover Stashed Changes
Find lost stashes:

```powershell
# Create stash
"stashed work" | Out-File stash.txt -Encoding UTF8
git add stash.txt
git stash

# View stash
git stash list
# stash@{0}: WIP on main

# Drop stash by mistake
git stash drop stash@{0}

# It's gone!
git stash list
# (empty)

# But reflog remembers!
git fsck --lost-found
# Or check reflog
git reflog show stash

# Recover
git stash apply <hash>
```

### Task 11: Find When Bug Was Introduced
Use reflog to find when things broke:

```powershell
# Your code is broken
# When did it break?

# View reflog
git reflog

# Check each state
git checkout HEAD@{5}
# Test... works!

git checkout HEAD@{3}
# Test... broken!

# Bug introduced between HEAD@{5} and HEAD@{3}
# Use git bisect from here (Exercise 16)
```

### Task 12: Clean Up Reflog
Understand reflog expiry:

```powershell
# Reflog keeps entries for 90 days by default
git config gc.reflogExpire
# 90

# Force garbage collection (removes old entries)
git reflog expire --expire=now --all
git gc --prune=now

# ⚠️ WARNING: This makes recovery impossible!
# Only do this if you're SURE you don't need history
```

### Task 13: Reflog for Collaboration Recovery
Recover from pull mistakes:

```powershell
# Before pull
git log --oneline -3
# abc1 commit 3
# abc2 commit 2
# abc3 commit 1

# Pull introduces messy merge
git pull origin main

# Messy merge commits everywhere!
# Want to undo the pull?

git reflog
# Find "pull origin main"
# Get hash before pull

# Reset to before pull
git reset --hard HEAD@{1}

# Try pull with rebase instead
git pull --rebase origin main
```

### Task 14: Recover Partial Work
Recover work from detached HEAD:

```powershell
# Checkout old commit
git checkout HEAD~3

# Make some changes (in detached HEAD)
"new feature" | Out-File new.js -Encoding UTF8
git add new.js
git commit -m "new feature in detached HEAD"

# Note the hash
git log --oneline -1
# xyz1234 new feature in detached HEAD

# Switch back to main (losing the commit!)
git checkout main

# The commit is "lost"
git log --oneline
# (doesn't show xyz1234)

# But reflog has it!
git reflog
# ... HEAD@{2}: commit: new feature in detached HEAD

# Recover by creating branch
git branch recovered-feature HEAD@{2}
# Or: git branch recovered-feature xyz1234

# Or cherry-pick the commit
git cherry-pick xyz1234
```

### Task 15: Complete Recovery Workflow
Full disaster recovery:

```powershell
# Simulate complete disaster
git checkout -b important-work
echo "Critical feature" > critical.js
git add critical.js
git commit -m "Critical feature"

echo "More critical work" >> critical.js
git commit -am "More critical work"

# Switch to main and delete branch
git checkout main
git branch -D important-work

# Also did a hard reset
git reset --hard HEAD~5

# PANIC! Everything is gone!

# RECOVER:
# 1. Check reflog
git reflog

# 2. Find the commits
# Find entries like:
# abc1234 HEAD@{2}: commit: More critical work
# def5678 HEAD@{3}: commit: Critical feature

# 3. Recover the branch
git checkout -b important-work-recovered abc1234

# 4. Verify
git log --oneline
# All commits back!

# 5. Merge to main if needed
git checkout main
git merge important-work-recovered
```

## ✅ Expected Outcome

You should be able to:
- Use reflog to see HEAD history
- Recover from hard resets
- Restore deleted branches
- Find and recover lost commits
- Time-travel through Git history
- Recover from rebase disasters

## 🎓 Key Concepts

### What is Reflog?

**Reflog = Reference Log**
- Records every change to HEAD (and other refs)
- Local to your repository (not pushed)
- Expires after 90 days (default)
- Your safety net for mistakes

### HEAD@{n} Syntax

```
HEAD@{0}  = Current HEAD
HEAD@{1}  = One move ago
HEAD@{2}  = Two moves ago
...
```

### Reflog vs Log

| `git log` | `git reflog` |
|-----------|--------------|
| Shows commit history | Shows HEAD movement history |
| Follows branch lineage | Shows all ref changes |
| Doesn't show lost commits | Shows "lost" commits |
| Public history | Local only |

## 🔍 Verification Commands

```powershell
# View reflog
git reflog

# View last 10 entries
git reflog -10

# View with dates
git reflog --date=iso

# Show reflog for branch
git reflog show main

# Find lost commits
git fsck --lost-found
```

## 💡 Pro Tips

### Safety First
```powershell
# Before dangerous operation, note current commit
git rev-parse HEAD

# Or create backup branch
git branch backup-$(date +%s)
```

### Reflog Aliases
```powershell
# Add to .gitconfig
git config --global alias.undo '!git reset --hard $(git rev-parse --abbrev-ref HEAD)@{1}'
git config --global alias.whoops '!git reflog'
```

### Search Reflog
```powershell
# Find specific commit message
git reflog | grep "important feature"

# Find recent checkouts
git reflog | grep "checkout"

# Find resets
git reflog | grep "reset"
```

## 🎯 What You Should Know Now

- ✅ What reflog is and how it works
- ✅ How to view reflog entries
- ✅ Recovering from hard resets
- ✅ Restoring deleted branches
- ✅ Finding lost commits
- ✅ Time-based HEAD references
- ✅ When reflog can't help (after gc)

## 📊 Reflog Commands Cheatsheet

```powershell
# View reflog
git reflog
git reflog show HEAD
git reflog show main

# View with dates
git reflog --date=relative
git reflog --date=iso

# HEAD references
HEAD@{0}                 # Current
HEAD@{1}                 # One ago
HEAD@{5}                 # Five ago
HEAD@{yesterday}         # Yesterday
HEAD@{1.hour.ago}        # Hour ago
HEAD@{2.weeks.ago}       # 2 weeks ago
HEAD@{2025-10-18}        # Specific date

# Recovery
git reset --hard HEAD@{1}        # Undo last operation
git branch branch-name HEAD@{3}  # Recover deleted branch
git cherry-pick <hash>           # Recover specific commit
git checkout -b new-branch <hash> # Create branch from lost commit

# Cleanup (dangerous!)
git reflog expire --expire=now --all
git gc --prune=now
```

## 🚨 Common Recovery Scenarios

### Scenario 1: Accidental Hard Reset
```powershell
git reset --hard HEAD~5  # Oops!
git reflog                # Find before reset
git reset --hard HEAD@{1} # Recover
```

### Scenario 2: Deleted Branch
```powershell
git branch -D feature     # Oops!
git reflog                # Find branch commits
git branch feature <hash> # Recreate
```

### Scenario 3: Lost Commits
```powershell
# Commits lost after checkout
git reflog                      # Find commits
git cherry-pick <hash>          # Recover them
```

### Scenario 4: Bad Merge
```powershell
git merge feature     # Messy merge!
git reflog            # Find before merge
git reset --hard HEAD@{1}  # Undo merge
```

## ⚠️ When Reflog Can't Help

Reflog can't recover:
- Untracked files (never added)
- Changes never committed
- After `git gc --prune=now`
- After 90 days (expired entries)
- In other people's repos (reflog is local)

## ⏭️ Next Exercise
Move on to **Exercise 18: Advanced Workflows** for team collaboration patterns!

---

**Time to Complete**: 35 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: Exercise 01-16  
**🔑 Key Takeaway**: Reflog is your safety net - nothing is truly lost in Git!
