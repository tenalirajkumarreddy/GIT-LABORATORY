# Solution: Exercise 19 - Scenario: Wrong Branch

## Complete Command Sequence

```powershell
mkdir wrong-branch-practice
cd wrong-branch-practice
git init
"Initial" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "Initial commit"

# Scenario 1: Committed to main instead of feature branch
git checkout main
"Feature work" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "feat: add feature"
# Oops! Should be on feature branch

# Solution 1a: Move to new branch
git log --oneline -1  # Note hash: abc1234
git branch feature abc1234
git reset --hard HEAD~1  # Remove from main
git checkout feature
# Feature work now on correct branch!

# Solution 1b: Using reflog
git reflog
git branch feature HEAD@{1}
git reset --hard HEAD~1

# Scenario 2: Multiple commits on wrong branch
git checkout main
"Work 1" | Out-File work1.txt -Encoding UTF8
git add work1.txt
git commit -m "Work 1"
"Work 2" | Out-File work2.txt -Encoding UTF8
git add work2.txt
git commit -m "Work 2"
"Work 3" | Out-File work3.txt -Encoding UTF8
git add work3.txt
git commit -m "Work 3"
# All should be on feature branch!

# Solution: Move all 3 commits
git log --oneline -3  # Note hashes
git branch feature-branch
git reset --hard HEAD~3
git checkout feature-branch
# All 3 commits moved!

# Scenario 3: Started work without creating branch
git checkout main
"Uncommitted work" | Out-File uncommitted.txt -Encoding UTF8
# Haven't committed yet, realized wrong branch

# Solution: Stash and move
git stash
git checkout -b correct-branch
git stash pop
git add uncommitted.txt
git commit -m "feat: add feature"

# Scenario 4: Committed to wrong feature branch
git checkout -b feature-a
git checkout -b feature-b
"Work for A" | Out-File work-a.txt -Encoding UTF8
git add work-a.txt
git commit -m "Work for A"
# Oops! On feature-b, should be feature-a

# Solution: Cherry-pick
git log --oneline -1  # Note hash: xyz7890
git checkout feature-a
git cherry-pick xyz7890
git checkout feature-b
git reset --hard HEAD~1
# Commit moved from B to A

# Scenario 5: Mixed commits on wrong branch
git checkout main
"Main work" | Out-File main-work.txt -Encoding UTF8
git add main-work.txt
git commit -m "Should be on main"  # Correct
"Feature work" | Out-File feat-work.txt -Encoding UTF8
git add feat-work.txt
git commit -m "Should be on feature"  # Wrong!
"More main" | Out-File main2.txt -Encoding UTF8
git add main2.txt
git commit -m "More main work"  # Correct

# Solution: Cherry-pick specific commit
git log --oneline -3  # Find feature commit hash
git checkout -b feature
git cherry-pick <feature-commit-hash>
git checkout main
git rebase -i HEAD~3
# Mark feature commit as "drop"

# Scenario 6: Pushed to wrong branch already
git checkout main
"Oops pushed" | Out-File oops.txt -Encoding UTF8
git add oops.txt
git commit -m "Oops"
# git push origin main  # Already pushed!

# Solution: Revert instead of reset
git revert HEAD
# git push origin main
git checkout -b correct-branch HEAD~1
# Commit now on correct branch, reverted on main

# Scenario 7: Haven't committed yet, wrong branch
git checkout main
"Staged work" | Out-File staged.txt -Encoding UTF8
git add staged.txt
# Realized wrong branch before commit

# Solution: Reset and move
git reset HEAD staged.txt
git checkout -b feature
git add staged.txt
git commit -m "feat: add feature"

# Scenario 8: Partial work on wrong branch
git checkout main
"File 1" | Out-File file1.txt -Encoding UTF8
"File 2" | Out-File file2.txt -Encoding UTF8
git add file1.txt file2.txt
git commit -m "Two files"
# file1 should be on main, file2 on feature

# Solution: Split the commit
git reset --soft HEAD~1
git reset HEAD file2.txt
git commit -m "Add file1"
git checkout -b feature
git add file2.txt
git commit -m "Add file2"

# Scenario 9: Merge to wrong branch
git checkout -b branch-a
"A work" | Out-File a.txt -Encoding UTF8
git add a.txt
git commit -m "A work"
git checkout -b branch-b
"B work" | Out-File b.txt -Encoding UTF8
git add b.txt
git commit -m "B work"
git checkout branch-a
git merge branch-b  # Oops! Shouldn't merge

# Solution: Reset merge
git reset --hard HEAD~1
# Or: git reset --hard HEAD@{1}

# Scenario 10: Hotfix on wrong branch
git checkout develop
"Urgent hotfix" | Out-File hotfix.txt -Encoding UTF8
git add hotfix.txt
git commit -m "Urgent hotfix"
# Should be on main!

# Solution: Cherry-pick to main
git log --oneline -1  # Note hash
git checkout main
git cherry-pick <hash>
# Now on both branches

# Scenario 11: Wrong branch, have uncommitted changes
git checkout main
"Changes" | Out-File changes.txt -Encoding UTF8
# Haven't added or committed

# Solution: Stash, switch, pop
git stash
git checkout -b feature
git stash pop
git add changes.txt
git commit -m "feat: changes"

# Scenario 12: Realized after several commits
git checkout main
1..5 | ForEach-Object {
    "Commit $_" | Out-File "file$_.txt" -Encoding UTF8
    git add "file$_.txt"
    git commit -m "Commit $_"
}
# All 5 should be on feature!

# Solution: Create branch, reset main
git branch feature main
git reset --hard HEAD~5
git checkout feature
# All 5 commits on feature, main clean

# Scenario 13: Conflicting work on wrong branch
git checkout feature-a
"Conflict content" | Out-File shared.txt -Encoding UTF8
git add shared.txt
git commit -m "Feature A work"
# Should be on feature-b

# Solution: Revert and redo
git revert HEAD
git checkout feature-b
"Correct content" | Out-File shared.txt -Encoding UTF8
git add shared.txt
git commit -m "Feature B work"

# Scenario 14: Accidental commit to detached HEAD
git checkout HEAD~3
"Detached work" | Out-File detached.txt -Encoding UTF8
git add detached.txt
git commit -m "Work in detached"
git log --oneline -1  # Note hash: def4567

git checkout main  # Lost!

# Solution: Create branch from reflog
git reflog
git branch recovered-work def4567
git checkout recovered-work

# Scenario 15: Complete recovery workflow
# Complex scenario: Multiple mistakes
git checkout main
"M1" | Out-File m1.txt -Encoding UTF8
git add m1.txt
git commit -m "Main 1"  # Correct

"F1" | Out-File f1.txt -Encoding UTF8
git add f1.txt
git commit -m "Feature 1"  # Wrong branch!

"M2" | Out-File m2.txt -Encoding UTF8
git add m2.txt
git commit -m "Main 2"  # Correct

"F2" | Out-File f2.txt -Encoding UTF8
git add f2.txt
git commit -m "Feature 2"  # Wrong branch!

# Solution: Interactive rebase to separate
git log --oneline -4
git rebase -i HEAD~4
# Reorder: M1, M2, F1, F2
# Then split:
git branch feature HEAD
git reset --hard HEAD~2
git checkout feature
git rebase main
```

## Key Strategies

### Before Committing
```powershell
git stash
git checkout correct-branch
git stash pop
```

### After One Commit
```powershell
git branch correct-branch
git reset --hard HEAD~1
git checkout correct-branch
```

### After Multiple Commits
```powershell
git branch correct-branch
git reset --hard HEAD~N
git checkout correct-branch
```

### Already Pushed
```powershell
# Don't reset! Use revert
git revert HEAD
git push
# Then move to correct branch
```

### Using Cherry-Pick
```powershell
git log --oneline -1  # Note hash
git checkout correct-branch
git cherry-pick <hash>
git checkout wrong-branch
git reset --hard HEAD~1
```

## Prevention Tips

### Always Check Current Branch
```powershell
# Before starting work
git branch  # Check current
# Or:
git status  # Shows branch
```

### Use Git Prompt
```powershell
# Configure prompt to show branch
# In PowerShell profile or bash
```

### Create Branch Immediately
```powershell
# Don't work on main
git checkout -b feature/name
# Then start working
```

### Use Aliases
```powershell
git config --global alias.whoami 'branch --show-current'
```

## Verification

✅ You should now be able to:
- Recognize wrong branch situations
- Move commits to correct branch
- Handle uncommitted changes
- Deal with pushed commits
- Prevent wrong branch commits

## Pro Tips

### Check Before Committing
```powershell
git branch --show-current
# Make it a habit!
```

### Reflog is Your Friend
```powershell
git reflog
# Can recover from almost anything
```

### Backup Before Complex Operations
```powershell
git branch backup
# Do complex recovery
# If problems:
git reset --hard backup
```

---

**Exercise completed!** ✅  
You can now recover from wrong branch commits!
