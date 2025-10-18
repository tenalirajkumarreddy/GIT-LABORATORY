# Solution: Exercise 13 - Cherry-Picking

## Complete Command Sequence

```powershell
# Preparation
mkdir cherry-pick-practice
cd cherry-pick-practice
git init
"Initial" | Out-File main.txt -Encoding UTF8
git add main.txt
git commit -m "Initial commit"

# Task 1: Basic cherry-pick
git checkout -b feature
"Feature A" | Out-File featureA.txt -Encoding UTF8
git add featureA.txt
git commit -m "Add feature A"
"Feature B" | Out-File featureB.txt -Encoding UTF8
git add featureB.txt
git commit -m "Add feature B"
git log --oneline  # Note feature A hash
git checkout main
git cherry-pick <feature-A-hash>
# Feature A is now on main!

# Task 2: Cherry-pick multiple commits
git checkout feature
"Feature C" | Out-File featureC.txt -Encoding UTF8
git add featureC.txt
git commit -m "Add feature C"
git log --oneline -3  # Note hashes
git checkout main
git cherry-pick <hash-B> <hash-C>
# Both commits applied

# Task 3: Cherry-pick range
git checkout feature
"D" | Out-File d.txt -Encoding UTF8
git add d.txt
git commit -m "D"
"E" | Out-File e.txt -Encoding UTF8
git add e.txt
git commit -m "E"
"F" | Out-File f.txt -Encoding UTF8
git add f.txt
git commit -m "F"
git checkout main
git cherry-pick <hash-D>..<hash-F>
# Applies D, E, F

# Task 4: Cherry-pick with conflicts
git checkout feature
"Conflict content" | Out-File main.txt -Encoding UTF8
git commit -am "Feature conflict"
git log --oneline -1  # Note hash
git checkout main
"Main content" | Out-File main.txt -Encoding UTF8
git commit -am "Main conflict"
git cherry-pick <conflict-hash>
# CONFLICT!
"Resolved content" | Out-File main.txt -Encoding UTF8
git add main.txt
git cherry-pick --continue

# Task 5: Abort cherry-pick
git checkout feature
"Another conflict" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Test"
git checkout main
"Main test" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Main test"
git cherry-pick <test-hash>  # Conflict!
git cherry-pick --abort
git status  # Clean

# Task 6: Cherry-pick without commit
git checkout feature
"No commit" | Out-File nocommit.txt -Encoding UTF8
git add nocommit.txt
git commit -m "No commit feature"
git log --oneline -1  # Note hash
git checkout main
git cherry-pick -n <no-commit-hash>
# Changes staged but not committed
git status
git commit -m "Cherry-picked and modified"

# Task 7: Cherry-pick and edit
git checkout feature
"Edit me" | Out-File edit.txt -Encoding UTF8
git add edit.txt
git commit -m "Edit feature"
git log --oneline -1
git checkout main
git cherry-pick --edit <edit-hash>
# Opens editor to change commit message

# Task 8: Cherry-pick with -x
git checkout feature
"Traceable" | Out-File trace.txt -Encoding UTF8
git add trace.txt
git commit -m "Traceable feature"
git log --oneline -1
git checkout main
git cherry-pick -x <trace-hash>
git log -1
# Shows "(cherry picked from commit ...)"

# Task 9: Cherry-pick from another branch
git checkout -b hotfix
"Urgent fix" | Out-File fix.txt -Encoding UTF8
git add fix.txt
git commit -m "Urgent fix"
git log --oneline -1  # Note hash
git checkout main
git cherry-pick <fix-hash>
# Hotfix now on main
git checkout feature
git cherry-pick <fix-hash>
# Hotfix now on feature too!

# Task 10: Cherry-pick merge commit
git checkout -b merge-test
"Merge A" | Out-File mergeA.txt -Encoding UTF8
git add mergeA.txt
git commit -m "Merge A"
git checkout main
"Main merge" | Out-File mainmerge.txt -Encoding UTF8
git add mainmerge.txt
git commit -m "Main merge"
git merge --no-ff merge-test
git log --oneline -1  # Note merge commit hash
git checkout feature
git cherry-pick -m 1 <merge-hash>
# -m 1 specifies which parent to use

# Task 11: Interactive cherry-pick
git checkout main
# Select commits interactively
git log --oneline feature
# Note multiple hashes
git cherry-pick <hash1>
git cherry-pick <hash2>
# Apply selectively

# Task 12: Cherry-pick to fix wrong branch
git checkout main
"Oops wrong branch" | Out-File oops.txt -Encoding UTF8
git add oops.txt
git commit -m "Should be on feature"
git log --oneline -1  # Note hash
git reset --hard HEAD~1  # Remove from main
git checkout feature
git cherry-pick <oops-hash>
# Now on correct branch!

# Task 13: Cherry-pick with strategy
git checkout feature
"Strategy test" | Out-File strategy.txt -Encoding UTF8
git add strategy.txt
git commit -m "Strategy"
git checkout main
git cherry-pick -X theirs <strategy-hash>
# Use their version on conflicts

# Task 14: View what will be cherry-picked
git log feature --oneline -5
git show <hash>  # Preview commit
git cherry-pick <hash>  # Apply

# Task 15: Complete workflow
# Bug found in main
git checkout main
"Bug fix" | Out-File bugfix.txt -Encoding UTF8
git add bugfix.txt
git commit -m "fix: critical bug"
git log --oneline -1  # Note hash

# Apply to all active branches
git checkout feature
git cherry-pick <bugfix-hash>
git checkout hotfix
git cherry-pick <bugfix-hash>
git checkout release
git cherry-pick <bugfix-hash>
# Bug fix now everywhere!
```

## Cherry-Pick Use Cases

### 1. Apply Specific Fix to Multiple Branches
```powershell
# Fix on main
git checkout main
git commit -m "fix: bug"
# Apply to release branches
git checkout release-1.0
git cherry-pick <fix-hash>
git checkout release-2.0
git cherry-pick <fix-hash>
```

### 2. Move Commit to Correct Branch
```powershell
# Committed to wrong branch
git log --oneline -1  # Note hash
git reset --hard HEAD~1
git checkout correct-branch
git cherry-pick <hash>
```

### 3. Backport Feature
```powershell
# Feature on develop
git checkout develop
# Note hash
git checkout release
git cherry-pick <feature-hash>
```

## Important Commands

```powershell
# Basic cherry-pick
git cherry-pick <commit>

# Multiple commits
git cherry-pick <hash1> <hash2> <hash3>

# Range of commits
git cherry-pick <start>..<end>

# With options
git cherry-pick -n <commit>      # Don't commit
git cherry-pick -x <commit>      # Add source reference
git cherry-pick --edit <commit>  # Edit message
git cherry-pick -m 1 <commit>    # Merge commit (parent 1)

# Conflict resolution
git cherry-pick --continue       # After resolving
git cherry-pick --abort          # Cancel
git cherry-pick --quit           # Stop but keep changes

# Strategy
git cherry-pick -X theirs <commit>
git cherry-pick -X ours <commit>
```

## Cherry-Pick vs Merge

| Operation | Cherry-Pick | Merge |
|-----------|-------------|-------|
| What | Copy specific commits | Combine branches |
| History | Creates new commits | Preserves history |
| Use when | Need specific changes | Want all changes |
| Result | Duplicate commits | Merged history |

## Common Workflows

### Hotfix to All Branches
```powershell
# Create hotfix on main
git checkout main
git commit -m "fix: critical"

# Apply to all release branches
for branch in release-1.0 release-2.0 release-3.0
do
    git checkout $branch
    git cherry-pick <fix-hash>
done
```

### Selective Feature Migration
```powershell
# Features on develop
git checkout develop
git log --oneline -10

# Pick specific features for release
git checkout release
git cherry-pick <feature-A-hash>
git cherry-pick <feature-C-hash>
# Skip feature B
```

## Verification

✅ You should now understand:
- Cherry-picking single commits
- Cherry-picking multiple commits
- Cherry-picking ranges
- Handling cherry-pick conflicts
- Cherry-pick use cases
- When to cherry-pick vs merge

## Pro Tips

### Preview Cherry-Pick
```powershell
git show <commit>  # See what will be applied
git log --oneline <branch>  # Find commits
```

### Cherry-Pick with Signoff
```powershell
git cherry-pick -s <commit>
# Adds "Signed-off-by" line
```

### Find Cherry-Picked Commits
```powershell
git log --grep="cherry picked from"
```

### Interactive Selection
```powershell
# Use gitk or other GUI
gitk
# Righ-click commits → Cherry-pick
```

### Avoid Duplicate Commits
```powershell
# Check if already applied
git branch --contains <commit>
```

---

**Exercise completed!** ✅  
You now master selective commit application!
