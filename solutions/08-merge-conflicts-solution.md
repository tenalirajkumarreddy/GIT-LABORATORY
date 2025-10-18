# Solution: Exercise 08 - Merge Conflicts

## Complete Command Sequence

```powershell
# Preparation
mkdir conflict-practice
cd conflict-practice
git init
"Initial content" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "Initial commit"

# Task 1: Create conflict
git checkout -b feature
"Feature version" | Out-File file.txt -Encoding UTF8
git commit -am "Feature changes"
git checkout main
"Main version" | Out-File file.txt -Encoding UTF8
git commit -am "Main changes"

# Task 2: Trigger conflict
git merge feature
# CONFLICT! Auto-merging file.txt

# Task 3: View conflict status
git status
# Shows "both modified: file.txt"

# Task 4: View conflict markers
Get-Content file.txt
# Shows:
# <<<<<<< HEAD
# Main version
# =======
# Feature version
# >>>>>>> feature

# Task 5: Resolve conflict manually
@"
Resolved version combining both changes
"@ | Out-File file.txt -Encoding UTF8
git add file.txt
git status  # No longer in conflict

# Task 6: Complete merge
git commit -m "Merge feature (resolved conflicts)"
git log --oneline --graph

# Task 7: Use merge tool
git checkout -b another-feature
"Another conflict" | Out-File conflict2.txt -Encoding UTF8
git add conflict2.txt
git commit -m "Another feature"
git checkout main
"Main conflict" | Out-File conflict2.txt -Encoding UTF8
git add conflict2.txt
git commit -m "Main changes"
git merge another-feature  # Conflict!
git mergetool  # Opens configured merge tool
# Resolve in tool, save, exit
git commit

# Task 8: View conflicts with diff3
git config merge.conflictstyle diff3
git checkout -b test-branch
"Test" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Test"
git checkout main
"Main test" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Main test"
git merge test-branch
# Shows three sections: ours, base, theirs

# Task 9: Abort conflicted merge
git merge --abort
git status  # Clean

# Task 10: Resolve keeping ours
git merge test-branch  # Conflict again
git checkout --ours test.txt
git add test.txt
git commit -m "Merge keeping our version"

# Task 11: Resolve keeping theirs
git checkout -b new-feature
"New" | Out-File new.txt -Encoding UTF8
git add new.txt
git commit -m "New feature"
git checkout main
"Main new" | Out-File new.txt -Encoding UTF8
git add new.txt
git commit -m "Main new"
git merge new-feature  # Conflict!
git checkout --theirs new.txt
git add new.txt
git commit -m "Merge keeping their version"

# Task 12: Multiple file conflicts
git checkout -b multi-conflict
"A" | Out-File a.txt -Encoding UTF8
"B" | Out-File b.txt -Encoding UTF8
"C" | Out-File c.txt -Encoding UTF8
git add .
git commit -m "Multi files"
git checkout main
"Main A" | Out-File a.txt -Encoding UTF8
"Main B" | Out-File b.txt -Encoding UTF8
git add .
git commit -m "Main multi"
git merge multi-conflict  # Multiple conflicts!
git status  # Shows all conflicted files

# Resolve each
"Resolved A" | Out-File a.txt -Encoding UTF8
"Resolved B" | Out-File b.txt -Encoding UTF8
git add a.txt b.txt
git commit -m "Resolve all conflicts"

# Task 13: View conflict history
git log --merge  # Commits involved in conflict
git diff  # Show conflicts
git diff --ours  # Against HEAD
git diff --theirs  # Against merging branch

# Task 14: Rerere (reuse recorded resolution)
git config rerere.enabled true
git checkout -b rerere-test
"Rerere content" | Out-File rerere.txt -Encoding UTF8
git add rerere.txt
git commit -m "Rerere test"
git checkout main
"Main rerere" | Out-File rerere.txt -Encoding UTF8
git add rerere.txt
git commit -m "Main rerere"
git merge rerere-test  # Conflict
"Resolved rerere" | Out-File rerere.txt -Encoding UTF8
git add rerere.txt
git commit -m "Resolve"
# Next time same conflict happens, Git auto-resolves!

# Task 15: Complex conflict resolution
git checkout -b complex
@"
function calculate() {
    // Feature version
    return x + y;
}
"@ | Out-File calc.js -Encoding UTF8
git add calc.js
git commit -m "Feature calc"
git checkout main
@"
function calculate() {
    // Main version
    return x * y;
}
"@ | Out-File calc.js -Encoding UTF8
git add calc.js
git commit -m "Main calc"
git merge complex  # Conflict!

# Resolve by combining both
@"
function calculate() {
    // Combined: addition and multiplication
    const sum = x + y;
    const product = x * y;
    return { sum, product };
}
"@ | Out-File calc.js -Encoding UTF8
git add calc.js
git commit -m "Merge: combine both calculations"
```

## Understanding Conflict Markers

```
<<<<<<< HEAD
Your current branch changes
=======
Incoming changes from merged branch
>>>>>>> branch-name
```

With diff3 style:
```
<<<<<<< HEAD
Your changes
||||||| merged common ancestor
Original content
=======
Their changes
>>>>>>> branch-name
```

## Resolution Strategies

### 1. Manual Resolution
```powershell
# Edit file, remove markers, keep what you want
# Then:
git add <file>
git commit
```

### 2. Choose Ours
```powershell
git checkout --ours <file>
git add <file>
git commit
```

### 3. Choose Theirs
```powershell
git checkout --theirs <file>
git add <file>
git commit
```

### 4. Use Merge Tool
```powershell
git mergetool
# Opens configured tool (VS Code, KDiff3, etc.)
```

## Important Commands

```powershell
# During conflict
git status              # See conflicted files
git diff                # Show conflicts
git merge --abort       # Cancel merge

# Resolution
git add <file>          # Mark as resolved
git commit              # Complete merge

# Strategies
git checkout --ours <file>    # Keep our version
git checkout --theirs <file>  # Keep their version
git mergetool                 # Use merge tool

# View conflict info
git log --merge         # Relevant commits
git diff --ours         # Against HEAD
git diff --theirs       # Against merging branch
```

## Common Conflict Patterns

### Pattern 1: Same Line Modified
```
HEAD:    print("Hello")
Theirs:  print("Hi there")
```
**Solution:** Choose one or combine

### Pattern 2: One Added, One Modified
```
HEAD:    // Modified line
Theirs:  // Added new line
```
**Solution:** Usually keep both

### Pattern 3: Both Deleted Different Content
```
HEAD:    (deleted A)
Theirs:  (deleted B)
```
**Solution:** Decide what to keep

## Verification

✅ You should now understand:
- What causes merge conflicts
- How to identify conflicted files
- Reading conflict markers
- Manual conflict resolution
- Using merge tools
- Keeping ours vs theirs
- Aborting conflicted merges

## Pro Tips

### Configure Merge Tool
```powershell
# Use VS Code
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Use KDiff3
git config --global merge.tool kdiff3

# Use P4Merge
git config --global merge.tool p4merge
```

### Enable Rerere
```powershell
git config --global rerere.enabled true
# Reuses conflict resolutions automatically
```

### Diff3 Conflict Style
```powershell
git config --global merge.conflictstyle diff3
# Shows original content too
```

### Prevent Conflicts
```powershell
# Pull with rebase instead of merge
git pull --rebase

# Keep feature branch updated
git checkout feature
git rebase main  # Regularly
```

---

**Exercise completed!** ✅  
You can now handle merge conflicts like a pro!
