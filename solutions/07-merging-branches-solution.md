# Solution: Exercise 07 - Merging Branches

## Complete Command Sequence

```powershell
# Preparation
mkdir merging-practice
cd merging-practice
git init
"Main content" | Out-File main.txt -Encoding UTF8
git add main.txt
git commit -m "Initial commit"

# Task 1: Fast-forward merge
git checkout -b feature
"Feature content" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "Add feature"
git checkout main
git merge feature
# Fast-forward! No merge commit created
git log --oneline --graph

# Task 2: Three-way merge
git checkout -b feature2
"Feature 2" | Out-File feature2.txt -Encoding UTF8
git add feature2.txt
git commit -m "Add feature 2"
git checkout main
"Main work" | Out-File main-work.txt -Encoding UTF8
git add main-work.txt
git commit -m "Main work"
git merge feature2
# Three-way merge creates merge commit
git log --oneline --graph

# Task 3: No fast-forward merge
git checkout -b feature3
"Feature 3" | Out-File feature3.txt -Encoding UTF8
git add feature3.txt
git commit -m "Add feature 3"
git checkout main
git merge --no-ff feature3 -m "Merge feature3"
# Always creates merge commit
git log --oneline --graph

# Task 4: View merge history
git log --oneline --graph --all
git log --merges  # Only merge commits
git show HEAD  # Show merge commit

# Task 5: Abort merge
git checkout -b feature4
"Feature 4" | Out-File conflict.txt -Encoding UTF8
git add conflict.txt
git commit -m "Feature 4"
git checkout main
"Main version" | Out-File conflict.txt -Encoding UTF8
git add conflict.txt
git commit -m "Main version"
git merge feature4  # Conflict!
git merge --abort  # Cancel merge
git status  # Clean

# Task 6: Merge specific commit
git checkout main
git log feature4 --oneline
git merge <commit-hash>  # Merge specific commit

# Task 7: Squash merge
git checkout -b feature5
"Commit 1" | Out-File f5.txt -Encoding UTF8
git add f5.txt
git commit -m "Commit 1"
"Commit 2" | Out-File f5.txt -Encoding UTF8
git commit -am "Commit 2"
"Commit 3" | Out-File f5.txt -Encoding UTF8
git commit -am "Commit 3"
git checkout main
git merge --squash feature5
git commit -m "Squashed feature5"
# All 3 commits became 1!

# Task 8: Check merge status
git branch --merged  # Branches merged into current
git branch --no-merged  # Branches not merged

# Task 9: Merge with strategy
git merge -s recursive feature6
git merge -s ours feature7  # Use our version on conflicts

# Task 10: Merge and delete branch
git checkout main
git merge feature5
git branch -d feature5  # Delete after merge

# Task 11: View merge base
git merge-base main feature2
# Shows common ancestor commit

# Task 12: Dry run merge
git merge --no-commit --no-ff feature6
git status  # See what would be merged
git merge --abort  # Cancel

# Task 13: Merge multiple branches
git merge feature2 feature3
# Octopus merge (multiple branches at once)

# Task 14: Fast-forward only
git merge --ff-only feature7
# Only merges if fast-forward possible
# Fails if three-way merge needed

# Task 15: Complete merge workflow
git checkout main
git pull  # Update main
git checkout -b feature/awesome
"Awesome feature" | Out-File awesome.txt -Encoding UTF8
git add awesome.txt
git commit -m "feat: add awesome feature"
git checkout main
git merge --no-ff feature/awesome -m "Merge feature/awesome"
git push  # Push merged result
git branch -d feature/awesome  # Cleanup
```

## Merge Types

### Fast-Forward Merge
```
Before:    main: A---B
           feature:    C---D

After:     main: A---B---C---D
           feature:        ^
```
No merge commit needed - just moves pointer forward.

### Three-Way Merge
```
Before:    main: A---B---C
           feature:    B---D---E

After:     main: A---B---C---M
           feature:    B---D---E---^
```
Creates merge commit M combining changes.

### Squash Merge
```
Before:    feature: C1---C2---C3

After:     main: ...---S
```
All commits squashed into single commit.

## Important Commands

```powershell
# Basic merge
git merge <branch>

# Merge strategies
git merge --ff           # Fast-forward if possible (default)
git merge --no-ff        # Always create merge commit
git merge --ff-only      # Only if fast-forward possible
git merge --squash       # Squash commits into one

# Merge control
git merge --abort        # Cancel merge
git merge --continue     # Continue after resolving conflicts
git merge --no-commit    # Merge but don't commit

# View merge info
git log --merges         # Show merge commits
git log --oneline --graph --all
git branch --merged      # Merged branches
git merge-base A B       # Common ancestor
```

## Merge Strategies

### When to Use Each

**Fast-Forward (default):**
- ✅ Simple, linear history
- ✅ Feature hasn't diverged from main
- ❌ Loses branch context

**No Fast-Forward (--no-ff):**
- ✅ Preserves branch history
- ✅ Shows when feature was merged
- ✅ Good for feature branches
- ❌ More merge commits

**Squash (--squash):**
- ✅ Clean history (one commit per feature)
- ✅ Removes WIP commits
- ❌ Loses detailed history

## Common Workflows

### Feature Branch Merge
```powershell
git checkout main
git pull
git checkout feature/login
git rebase main  # Optional: update feature
git checkout main
git merge --no-ff feature/login
git push
git branch -d feature/login
```

### Hotfix Merge
```powershell
git checkout main
git checkout -b hotfix/critical
# ... fix ...
git commit -am "fix: critical bug"
git checkout main
git merge hotfix/critical
git tag v1.0.1
git push --tags
git branch -d hotfix/critical
```

## Verification

✅ You should now understand:
- Fast-forward vs three-way merges
- When to use --no-ff
- Squash merging
- Merge strategies
- Viewing merge history
- Aborting merges

## Pro Tips

### Check Before Merging
```powershell
# What will be merged?
git diff main..feature

# How many commits?
git log main..feature --oneline

# Test merge without committing
git merge --no-commit --no-ff feature
git status
git merge --abort
```

### Clean Merge History
```powershell
# See merge graph
git log --oneline --graph --all --decorate

# Find merge commits
git log --merges --oneline

# See what was merged
git show <merge-commit>
```

### Undo Merge
```powershell
# If not pushed yet
git reset --hard HEAD~1

# If already pushed
git revert -m 1 <merge-commit>
```

---

**Exercise completed!** ✅  
You now master merging branches!
