# Solution: Exercise 15 - Interactive Rebase

## Complete Command Sequence

```powershell
# Preparation
mkdir interactive-rebase-practice
cd interactive-rebase-practice
git init

# Create messy history
"function add(a, b) { return a + b; }" | Out-File math.js -Encoding UTF8
git add math.js
git commit -m "add function"
"function subtract(a, b) { return a - b; }" | Add-Content math.js
git commit -am "WIP"
"// TODO: test this" | Add-Content math.js
git commit -am "typo fix"
"function multiply(a, b) { return a * b; }" | Add-Content math.js
git commit -am "fixed bug"

# Task 1: View the mess
git log --oneline
# Shows unprofessional messages

# Task 2: Start interactive rebase
git rebase -i HEAD~4
# Editor opens with pick commands

# Task 3: Reword commit messages
# In editor, change:
# pick abc1234 add function
# reword def5678 WIP
# pick ghi9012 typo fix
# Save, then change "WIP" to "feat: add subtract function"

# Task 4: Squash multiple commits
git rebase -i HEAD~4
# Change to:
# pick abc1234 add function
# squash def5678 feat: add subtract function
# squash ghi9012 typo fix
# squash jkl3456 fixed bug
# Save, then write combined message

# Task 5: Fixup (squash without message)
"export { add };" | Out-File exports.js -Encoding UTF8
git add exports.js
git commit -m "feat: add exports"
"export { add, subtract };" | Out-File exports.js -Encoding UTF8
git commit -am "oops forgot some"
git rebase -i HEAD~2
# Change second to: fixup
# Message automatically discarded

# Task 6: Reorder commits
"const PI = 3.14159;" | Out-File constants.js -Encoding UTF8
git add constants.js
git commit -m "feat: add constants"
"# Math Library" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "docs: add README"
git rebase -i HEAD~2
# Swap the two lines in editor
# README commit now comes first

# Task 7: Drop unwanted commits
"console.log('debug');" | Add-Content math.js
git commit -am "debug: temp logging"
git rebase -i HEAD~1
# Change to: drop
# Or delete the line entirely
# Commit is gone

# Task 8: Edit a commit (split it)
"function sqrt(x) { return Math.sqrt(x); }
function pow(x, y) { return Math.pow(x, y); }" | Out-File advanced.js -Encoding UTF8
git add advanced.js
git commit -m "add advanced functions"
git rebase -i HEAD~1
# Change to: edit
# When stops:
git reset HEAD~1
"function sqrt(x) { return Math.sqrt(x); }" | Out-File advanced.js -Encoding UTF8
git add advanced.js
git commit -m "feat: add sqrt function"
"function pow(x, y) { return Math.pow(x, y); }" | Out-File advanced.js -Encoding UTF8
git commit -am "feat: add pow function"
git rebase --continue

# Task 9: Reword multiple messages
git rebase -i HEAD~5
# Change multiple to reword:
# reword abc1234 added feature x
# reword def5678 fixed something
# Then fix each message with proper format

# Task 10: Exec command
git rebase -i HEAD~3
# Add exec lines:
# pick abc1234 feat: add function
# exec npm test
# pick def5678 fix: fix bug
# exec npm test
# Runs tests after each commit

# Task 11: Rebase with conflicts
git checkout -b feature
"new feature" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "feat: add feature"
git checkout main
"main work" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "feat: different feature"
git checkout feature
git rebase main  # Conflict!
"Resolved" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git rebase --continue

# Task 12: Autosquash workflow
"function test() {}" | Out-File test.js -Encoding UTF8
git add test.js
git commit -m "feat: add test function"
"function test() { return true; }" | Out-File test.js -Encoding UTF8
git commit -am "fixup! feat: add test function"
git rebase -i --autosquash HEAD~2
# Git automatically marks the fixup!

# Task 13: Rebase onto different base
git checkout main
"v1" | Out-File main.txt -Encoding UTF8
git add main.txt
git commit -m "v1"
git checkout -b feature
"feature" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "feat"
git checkout main
"v2" | Out-File main.txt -Encoding UTF8
git commit -am "v2"
"v3" | Out-File main.txt -Encoding UTF8
git commit -am "v3"
git checkout feature
git rebase --onto main~1 main
# Feature now based on v2

# Task 14: Complete cleanup workflow
# Start with messy 10 commits
git log --oneline -10
git rebase -i HEAD~10
# Organize:
# pick commit1 feat: start feature
# squash commit2 WIP
# squash commit3 more work
# fixup commit4 typo
# reword commit5 added tests
# squash commit6 more tests
# squash commit7 docs
# pick commit8 fix: bug found
# fixup commit9 oops
# drop commit10 debug
# Result: 3 clean commits

# Task 15: Backup before rebase
git branch backup
git rebase -i HEAD~5
# If disaster:
git reset --hard backup
```

## Interactive Rebase Commands

```
pick (p)   - Use commit as-is
reword (r) - Change commit message
edit (e)   - Stop to amend commit
squash (s) - Merge into previous, keep both messages
fixup (f)  - Merge into previous, discard this message
drop (d)   - Remove commit
exec (x)   - Run shell command
```

## When to Use Interactive Rebase

### ✅ Good Use Cases
- Clean up local commits before pushing
- Squash "fix typo" commits
- Rewrite unprofessional messages
- Combine related commits
- Remove debug commits
- Reorder commits logically

### ❌ Don't Use
- Commits already pushed to shared remote
- Public branch history
- Commits others based work on
- Main/master branch

## Important Commands

```powershell
# Start interactive rebase
git rebase -i HEAD~N        # Last N commits
git rebase -i <commit>      # From specific commit
git rebase -i main          # All commits in branch

# During rebase
git rebase --continue       # After resolving conflict
git rebase --abort          # Cancel rebase
git rebase --skip           # Skip current commit

# Autosquash
git commit --fixup=<hash>
git rebase -i --autosquash HEAD~N

# Config
git config --global rebase.autosquash true
```

## Common Patterns

### Squash All WIP Commits
```powershell
git rebase -i HEAD~5
# Mark all WIP commits as squash
```

### Clean Commit Messages
```powershell
git rebase -i HEAD~10
# Reword all unprofessional messages
```

### Remove Debug Commits
```powershell
git rebase -i HEAD~5
# Drop or delete debug commit lines
```

## Verification

✅ You should now understand:
- All interactive rebase commands
- Squashing vs fixup
- Reordering commits
- Splitting commits
- When NOT to rebase
- Handling conflicts

## Pro Tips

### Safe Rebase
```powershell
git branch backup
git rebase -i HEAD~5
# If problems:
git reset --hard backup
```

### Configure Editor
```powershell
git config --global core.editor "code --wait"
```

### Enable Autosquash by Default
```powershell
git config --global rebase.autosquash true
```

### Auto-stash Before Rebase
```powershell
git config --global rebase.autoStash true
```

### Abort vs Continue
```powershell
git rebase --abort   # Undo everything
git rebase --continue # After fixing
git rebase --skip    # Skip current
```

---

**Exercise completed!** ✅  
You now master interactive rebase for clean history!
