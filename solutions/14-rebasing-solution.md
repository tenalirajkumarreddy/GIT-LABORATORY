# Solution: Exercise 14 - Rebasing

## Complete Command Sequence

```powershell
# Preparation
mkdir rebasing-practice
cd rebasing-practice
git init
"Initial" | Out-File main.txt -Encoding UTF8
git add main.txt
git commit -m "Initial commit"

# Task 1: Basic rebase
"Main A" | Out-File main.txt -Encoding UTF8
git commit -am "Main A"
git checkout -b feature
"Feature 1" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "Feature 1"
git checkout main
"Main B" | Out-File main.txt -Encoding UTF8
git commit -am "Main B"
git checkout feature
git rebase main
# Feature commits now on top of Main B
git log --oneline --graph --all

# Task 2: Rebase with conflicts
git checkout main
"Conflict line" | Out-File conflict.txt -Encoding UTF8
git add conflict.txt
git commit -m "Main conflict"
git checkout feature
"Feature conflict" | Out-File conflict.txt -Encoding UTF8
git add conflict.txt
git commit -m "Feature conflict"
git rebase main  # CONFLICT!
"Resolved" | Out-File conflict.txt -Encoding UTF8
git add conflict.txt
git rebase --continue

# Task 3: Abort rebase
git checkout -b test-branch
"Test" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Test"
git checkout main
"Main test" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Main test"
git checkout test-branch
git rebase main  # Conflict!
git rebase --abort
git status  # Clean

# Task 4: Rebase onto specific base
git checkout -b feature2
"F2" | Out-File f2.txt -Encoding UTF8
git add f2.txt
git commit -m "F2"
git checkout main
"M1" | Out-File m1.txt -Encoding UTF8
git add m1.txt
git commit -m "M1"
"M2" | Out-File m2.txt -Encoding UTF8
git add m2.txt
git commit -m "M2"
git checkout feature2
git rebase --onto main~1 main
# Rebases onto M1 instead of M2

# Task 5: Interactive rebase
git checkout -b interactive
"C1" | Out-File c1.txt -Encoding UTF8
git add c1.txt
git commit -m "Commit 1"
"C2" | Out-File c2.txt -Encoding UTF8
git add c2.txt
git commit -m "Commit 2"
"C3" | Out-File c3.txt -Encoding UTF8
git add c3.txt
git commit -m "Commit 3"
git rebase -i HEAD~3
# Opens editor - can reorder, squash, edit

# Task 6: Skip commit during rebase
git checkout main
"Skip me" | Out-File skip.txt -Encoding UTF8
git add skip.txt
git commit -m "Skip"
git checkout feature
git rebase main
# If conflict you want to skip:
# git rebase --skip

# Task 7: Rebase vs merge comparison
git checkout -b rebase-demo
"Rebase work" | Out-File rebase.txt -Encoding UTF8
git add rebase.txt
git commit -m "Rebase work"
git checkout main
"Main work" | Out-File mainwork.txt -Encoding UTF8
git add mainwork.txt
git commit -m "Main work"

# Merge (keeps history)
git checkout -b merge-demo rebase-demo
git merge main
git log --graph --oneline

# Rebase (linear history)
git checkout rebase-demo
git rebase main
git log --graph --oneline
# Compare: rebase is linear!

# Task 8: Rebase preserving merges
git checkout -b preserve-merges
"Work" | Out-File work.txt -Encoding UTF8
git add work.txt
git commit -m "Work"
git checkout -b sub-feature
"Sub" | Out-File sub.txt -Encoding UTF8
git add sub.txt
git commit -m "Sub"
git checkout preserve-merges
git merge sub-feature
git rebase -r main  # -r = --rebase-merges
# Preserves merge commits

# Task 9: Autosquash
git checkout -b autosquash-demo
"Original" | Out-File auto.txt -Encoding UTF8
git add auto.txt
git commit -m "Original commit"
"Fix for original" | Out-File auto.txt -Encoding UTF8
git commit -am "fixup! Original commit"
git rebase -i --autosquash HEAD~2
# Automatically marks fixup!

# Task 10: Rebase with strategy
git checkout main
"Strategy" | Out-File strategy.txt -Encoding UTF8
git add strategy.txt
git commit -m "Strategy"
git checkout feature
"Feature strategy" | Out-File strategy.txt -Encoding UTF8
git add strategy.txt
git commit -m "Feature strategy"
git rebase -X theirs main
# Uses their version on conflicts

# Task 11: Pull with rebase
# Simulated workflow
git checkout main
# Normally: git pull --rebase origin main
# Instead of: git pull origin main
# Avoids merge commits from pulls

# Task 12: Rebase to squash commits
git checkout -b squash-feature
"S1" | Out-File s1.txt -Encoding UTF8
git add s1.txt
git commit -m "S1"
"S2" | Out-File s2.txt -Encoding UTF8
git add s2.txt
git commit -m "S2"
"S3" | Out-File s3.txt -Encoding UTF8
git add s3.txt
git commit -m "S3"
git rebase -i HEAD~3
# Mark all but first as "squash"
# Results in one commit

# Task 13: Rebase to reorder commits
git checkout -b reorder
"A" | Out-File a.txt -Encoding UTF8
git add a.txt
git commit -m "A"
"B" | Out-File b.txt -Encoding UTF8
git add b.txt
git commit -m "B"
"C" | Out-File c.txt -Encoding UTF8
git add c.txt
git commit -m "C"
git rebase -i HEAD~3
# Reorder lines in editor: C, A, B

# Task 14: Rebase to edit commit
git checkout -b edit-commit
"Edit me" | Out-File edit.txt -Encoding UTF8
git add edit.txt
git commit -m "Edit me"
"More" | Out-File more.txt -Encoding UTF8
git add more.txt
git commit -m "More"
git rebase -i HEAD~2
# Mark first as "edit"
# Make changes
"Edited" | Out-File edit.txt -Encoding UTF8
git add edit.txt
git commit --amend --no-edit
git rebase --continue

# Task 15: Complete rebase workflow
# Feature branch workflow
git checkout main
git pull
git checkout -b feature/new-thing
"Work 1" | Out-File work1.txt -Encoding UTF8
git add work1.txt
git commit -m "WIP"
"Work 2" | Out-File work2.txt -Encoding UTF8
git add work2.txt
git commit -m "More WIP"
"Work 3" | Out-File work3.txt -Encoding UTF8
git add work3.txt
git commit -m "Final work"

# Clean up before merging
git rebase -i HEAD~3  # Squash WIP commits
git rebase main       # Update with latest main
# Now ready for PR/merge
```

## Rebase vs Merge

### Merge
```
Before:    main: A---B---C
           feature:  B---D---E

After:     main: A---B---C---M
                      \     /
           feature:    D---E
```
Preserves exact history, creates merge commit.

### Rebase
```
Before:    main: A---B---C
           feature:  B---D---E

After:     main: A---B---C
           feature:      D'---E'
```
Linear history, rewrites commits.

## Important Commands

```powershell
# Basic rebase
git rebase <branch>
git rebase main

# Interactive rebase
git rebase -i <base>
git rebase -i HEAD~3

# Conflict handling
git rebase --continue
git rebase --abort
git rebase --skip

# Advanced options
git rebase --onto <newbase> <upstream> <branch>
git rebase -r main  # Preserve merges
git rebase -i --autosquash
git rebase -X theirs main  # Strategy

# Pull with rebase
git pull --rebase
git pull --rebase=preserve
```

## When to Use Rebase

### ✅ Good Use Cases
- Clean up local commits before pushing
- Keep feature branch updated with main
- Create linear history
- Squash WIP commits
- Reorder commits logically

### ❌ Don't Rebase
- Public/shared branches
- Commits already pushed to shared remote
- Main/master branch
- When working with others on same branch

## Interactive Rebase Commands

```
pick   = use commit
reword = use commit, but edit message
edit   = use commit, but stop for amending
squash = use commit, merge into previous
fixup  = like squash, discard message
drop   = remove commit
exec   = run shell command
```

## Common Workflows

### Update Feature Branch
```powershell
git checkout feature
git fetch origin
git rebase origin/main
# Feature now based on latest main
```

### Clean Up Before PR
```powershell
git checkout feature
git rebase -i main
# Squash WIP commits
# Reword messages
# Reorder commits
```

### Sync with Upstream (Fork)
```powershell
git fetch upstream
git checkout main
git rebase upstream/main
git push origin main
```

## Verification

✅ You should now understand:
- Basic rebasing
- Interactive rebase
- Rebase vs merge
- Handling rebase conflicts
- When to rebase
- When NOT to rebase

## Pro Tips

### Safe Rebase
```powershell
# Create backup branch
git branch backup

# Do rebase
git rebase main

# If disaster:
git reset --hard backup
```

### Config for Always Rebase on Pull
```powershell
git config --global pull.rebase true
# Or per-repo:
git config pull.rebase true
```

### Autostash During Rebase
```powershell
git config --global rebase.autoStash true
# Automatically stashes/unstashes
```

### Find Base Commit
```powershell
git merge-base feature main
# Shows where feature branched from main
```

---

**Exercise completed!** ✅  
You now understand rebasing and linear history!
