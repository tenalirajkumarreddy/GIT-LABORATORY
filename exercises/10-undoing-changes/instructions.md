# Exercise 10: Undoing Changes - Reset, Revert, Restore

## 🎯 Objective
Master all methods of undoing changes in Git - from safe reversions to dangerous history rewrites.

## 📚 Concepts Covered
- `git revert` - Safe undo (creates new commit)
- `git reset --soft` - Undo commit, keep changes staged
- `git reset --mixed` - Undo commit, unstage changes
- `git reset --hard` - Undo commit, discard changes
- `git restore` - Discard working directory changes
- Understanding the differences and when to use each

## 📝 Preparation

```powershell
mkdir undo-mastery
cd undo-mastery
git init

# Create a history
"Version 1" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Version 1"

"Version 2" | Out-File app.js -Encoding UTF8
git commit -am "Version 2"

"Version 3" | Out-File app.js -Encoding UTF8
git commit -am "Version 3"

"Version 4" | Out-File app.js -Encoding UTF8
git commit -am "Version 4"

"Version 5" | Out-File app.js -Encoding UTF8
git commit -am "Version 5"
```

## 📝 Tasks

### Task 1: Git Revert (Safe Undo for Pushed Commits)
1. View your commit history: `git log --oneline`
2. Revert the last commit: `git revert HEAD`
3. An editor opens for commit message (keep default)
4. View log - see new "Revert" commit created
5. Check app.js - back to Version 4
6. Key insight: History NOT rewritten, new commit added

### Task 2: Revert Older Commits
1. View log and note the hash of "Version 3" commit
2. Revert that specific commit: `git revert <hash>`
3. View log - another revert commit added
4. Check app.js content
5. Understanding: Can revert any commit, not just recent ones

### Task 3: Revert Multiple Commits
1. View your current history
2. Revert last 2 commits at once: `git revert HEAD~2..HEAD`
3. This creates 2 separate revert commits
4. Alternatively, revert range into one commit: `git revert -n HEAD~2..HEAD` then `git commit`

### Task 4: Reset --soft (Undo Commit, Keep Changes Staged)
1. Create new file: `"Test" | Out-File test.js -Encoding UTF8`
2. Commit: `git commit -am "Add test"`
3. View log - test commit is there
4. Reset soft: `git reset --soft HEAD~1`
5. Check status - test.js is STAGED
6. Check log - commit is gone
7. Commit again with better message if you want

### Task 5: Reset --mixed (Undo Commit, Unstage Changes)
1. Create file: `"Feature" | Out-File feature.js -Encoding UTF8`
2. Stage and commit: "Add feature"
3. Reset mixed (default): `git reset HEAD~1`
4. Check status - feature.js is UNSTAGED but exists
5. Check log - commit is gone
6. Changes are in working directory, ready to restage

### Task 6: Reset --hard (Undo Commit, Discard Everything)
1. Make several commits with actual content
2. View log to see commits
3. Reset hard to 2 commits ago: `git reset --hard HEAD~2`
4. Check log - those commits are GONE
5. Check files - changes are GONE
6. ⚠️ This is DANGEROUS - cannot undo!

### Task 7: Understanding Reset Modes Comparison
Create this scenario:
1. Make a commit with new file
2. Try all three reset modes on separate branches to compare:

```powershell
# Setup
git checkout -b test-soft
"Test" | Out-File demo.js -Encoding UTF8
git add demo.js
git commit -m "Test commit"

# Soft reset
git reset --soft HEAD~1
git status  # File is staged
git checkout -b test-mixed main
"Test" | Out-File demo.js -Encoding UTF8
git add demo.js
git commit -m "Test commit"

# Mixed reset
git reset HEAD~1  # or --mixed
git status  # File is unstaged

git checkout -b test-hard main
"Test" | Out-File demo.js -Encoding UTF8
git add demo.js
git commit -m "Test commit"

# Hard reset
git reset --hard HEAD~1
git status  # File is gone!
```

### Task 8: Reset to Specific Commit
1. View log with hashes
2. Copy hash of an older commit
3. Reset to that specific commit: `git reset --hard <hash>`
4. Everything after that commit is gone
5. View log to confirm

### Task 9: Undo a Reset (Using Reflog)
1. View reflog: `git reflog`
2. Find the commit before your last reset
3. Reset back to it: `git reset --hard HEAD@{1}`
4. You've recovered! Reflog saves you
5. This is how to undo a "permanent" hard reset

### Task 10: Restore vs Reset
1. Modify a file but don't commit
2. Restore it: `git restore filename` (discards working directory changes)
3. Stage a file: `git add filename`
4. Unstage with restore: `git restore --staged filename`
5. Unstage with reset: `git reset HEAD filename`
6. Both work for unstaging, but restore is clearer

### Task 11: Amending Last Commit
1. Make a commit with typo: "Add feture"
2. Realize mistake immediately
3. Amend: `git commit --amend -m "Add feature"`
4. Or add forgotten file:
   ```powershell
   git commit -m "Add feature"
   "Forgotten" | Out-File forgot.js -Encoding UTF8
   git add forgot.js
   git commit --amend --no-edit
   ```

### Task 12: Real-World Scenario - Wrong Branch
1. Make commits on main (pretend it was wrong branch)
2. Create correct branch: `git branch correct-branch`
3. Reset main back: `git reset --hard HEAD~3`
4. Switch to correct-branch: `git checkout correct-branch`
5. Commits are saved there!

### Task 13: Revert vs Reset Decision
Practice deciding which to use:

```
Scenario 1: Commit already pushed to shared repo
Answer: REVERT (don't rewrite shared history)

Scenario 2: Local commit, not pushed
Answer: RESET (can rewrite local history safely)

Scenario 3: Want to keep history transparent
Answer: REVERT (shows what happened)

Scenario 4: Want clean history, not pushed
Answer: RESET (or interactive rebase)
```

## ✅ Expected Outcome

You should know:
- When to use revert vs reset vs restore
- The three reset modes and their effects
- How to recover from mistakes using reflog
- How to amend commits
- Safe vs dangerous operations

## 🎓 Key Concepts

### The Three Reset Modes

| Mode | Commit | Staging | Working Dir | Use Case |
|------|--------|---------|-------------|----------|
| --soft | ✅ Undo | ❌ Keep | ❌ Keep | Redo commit message |
| --mixed | ✅ Undo | ✅ Undo | ❌ Keep | Unstage changes |
| --hard | ✅ Undo | ✅ Undo | ✅ Undo | Complete do-over |

### Revert vs Reset

**Revert:**
- ✅ Safe for pushed commits
- ✅ Preserves history
- ✅ Creates new commit
- ❌ History has "noise"

**Reset:**
- ❌ Dangerous for pushed commits
- ❌ Rewrites history
- ❌ Removes commits
- ✅ Clean history

## 🔍 Verification Commands

```powershell
# Check what reset would do (doesn't actually reset)
git reset --soft HEAD~1 --dry-run

# View reflog to see all HEAD movements
git reflog

# See what would be lost
git diff HEAD~3 HEAD

# Check current state
git status
git log --oneline
```

## 💡 Pro Tips

### Golden Rule
```
Never reset commits that have been pushed to shared repository!
Use revert instead.
```

### Quick Reference
```powershell
# Undo last commit, keep changes
git reset --soft HEAD~1

# Undo last commit, unstage changes
git reset HEAD~1

# Undo last commit, discard everything
git reset --hard HEAD~1

# Revert a pushed commit (safe)
git revert HEAD

# Amend last commit
git commit --amend

# Recover from reset
git reflog
git reset --hard HEAD@{1}
```

### VS Code Integration
- VS Code shows uncommitted changes
- Can discard from UI (same as git restore)
- Can unstage from UI (same as git restore --staged)

## ⚠️ Warning Signs

🚨 **DON'T reset if:**
- Commits are pushed to remote
- Others have pulled your commits
- Working on shared branch

✅ **OK to reset if:**
- Commits only local
- Working on feature branch alone
- Want to clean up before pushing

## 🎯 What You Should Know Now

- ✅ How git revert works (safe)
- ✅ Three reset modes (soft, mixed, hard)
- ✅ When to use each method
- ✅ How to recover from mistakes
- ✅ How to amend commits
- ✅ The golden rule about shared history

## 📊 Decision Tree

```
Need to undo a change?
│
├─ Is it pushed to shared repo?
│  ├─ YES → Use revert ✅
│  └─ NO → Continue...
│
├─ Just want to change commit message?
│  └─ Use commit --amend ✅
│
├─ Want to redo commit with more changes?
│  └─ Use reset --soft ✅
│
├─ Want to unstage but keep changes?
│  └─ Use reset --mixed (or restore --staged) ✅
│
└─ Want to completely discard?
   └─ Use reset --hard ⚠️ (dangerous!)
```

## ⏭️ Next Exercise
Ready for **Exercise 11: Stashing** - temporarily save your work!

---

**Time to Complete**: 40 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-09
