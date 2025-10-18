# Exercise 15: Interactive Rebase - Rewriting History Like a Pro

## 🎯 Objective
Master interactive rebase to clean up commit history, squash commits, reword messages, and reorder commits.

## 📚 Concepts Covered
- Interactive rebase (`git rebase -i`)
- Squashing commits
- Fixup commits
- Reordering commits
- Editing commits
- Rewording commit messages
- Dropping commits
- Splitting commits

## ⚠️ Warning
**NEVER rebase commits that have been pushed to a shared remote!**  
Only rebase local commits or personal branches.

## 📝 Preparation

```powershell
mkdir interactive-rebase-practice
cd interactive-rebase-practice
git init

# Create messy commit history (like real development)
"function add(a, b) { return a + b; }" | Out-File math.js -Encoding UTF8
git add math.js
git commit -m "add function"

"function subtract(a, b) { return a - b; }" | Add-Content math.js
git commit -am "WIP"

"// TODO: test this" | Add-Content math.js
git commit -am "typo fix"

"function multiply(a, b) { return a * b; }" | Add-Content math.js
git commit -am "fixed bug"

"function divide(a, b) { return a / b; }" | Add-Content math.js
git commit -am "forgot to commit this earlier"

"// Math utilities v1.0" | Out-File math.js -Append -Encoding UTF8
git commit -am "oops another fix"
```

## 📝 Tasks

### Task 1: View the Mess
1. See log: `git log --oneline`
2. Notice:
   - "WIP" (not descriptive)
   - "typo fix" (what typo?)
   - "oops another fix" (unprofessional)
   - Multiple small commits that should be one
3. Let's clean this up!

### Task 2: Start Interactive Rebase
1. Rebase last 6 commits: `git rebase -i HEAD~6`
2. Your default editor opens with:
   ```
   pick abc1234 add function
   pick def5678 WIP
   pick ghi9012 typo fix
   pick jkl3456 fixed bug
   pick mno7890 forgot to commit this earlier
   pick pqr1234 oops another fix
   ```
3. Don't save yet! Let's understand commands first.

### Task 3: Understanding Rebase Commands
Available commands in interactive rebase:
- `pick` (p): Use commit as-is
- `reword` (r): Change commit message
- `edit` (e): Stop to amend commit
- `squash` (s): Merge into previous commit, keep both messages
- `fixup` (f): Merge into previous commit, discard this message
- `drop` (d): Remove commit
- `exec` (x): Run shell command

### Task 4: Reword Commit Messages
1. Start rebase: `git rebase -i HEAD~6`
2. Change lines:
   ```
   pick abc1234 add function
   reword def5678 WIP
   pick ghi9012 typo fix
   pick jkl3456 fixed bug
   pick mno7890 forgot to commit this earlier
   pick pqr1234 oops another fix
   ```
3. Save and close
4. Editor opens for "WIP" commit
5. Change to: "feat: add subtract function"
6. Save and close
7. Check log: `git log --oneline`

### Task 5: Squash Multiple Commits
Clean up by combining related commits:

```powershell
# Start fresh
git rebase -i HEAD~6

# Change to:
pick abc1234 add function
squash def5678 feat: add subtract function
squash ghi9012 typo fix
squash jkl3456 fixed bug
squash mno7890 forgot to commit this earlier
squash pqr1234 oops another fix

# Save. Editor opens with combined message.
# Clean it up to:
feat: add math utility functions

- Add addition function
- Add subtraction function  
- Add multiplication function
- Add division function

# Save. Check log - one clean commit!
```

### Task 6: Fixup (Squash Without Message)
Create new scenario:

```powershell
"export { add, subtract };" | Out-File exports.js -Encoding UTF8
git add exports.js
git commit -m "feat: add exports"

"export { add, subtract, multiply, divide };" | Out-File exports.js -Encoding UTF8
git commit -am "oops forgot some exports"

# Rebase
git rebase -i HEAD~2

# Change to:
pick abc1234 feat: add exports
fixup def5678 oops forgot some exports

# Save. The second commit merges in without its message!
```

### Task 7: Reorder Commits
Sometimes commits are in wrong order:

```powershell
"const PI = 3.14159;" | Out-File constants.js -Encoding UTF8
git add constants.js
git commit -m "feat: add constants"

"# Math Library" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "docs: add README"

"const E = 2.71828;" | Add-Content constants.js
git commit -am "feat: add more constants"

# Rebase to reorder
git rebase -i HEAD~3

# Reorder by moving lines:
pick abc1234 docs: add README  # <-- Moved up
pick def5678 feat: add constants
pick ghi9012 feat: add more constants

# Save. Commits are now in logical order!
```

### Task 8: Drop Unwanted Commits
Remove commits completely:

```powershell
"console.log('debug');" | Add-Content math.js
git commit -am "debug: temp logging"

"console.log('more debug');" | Add-Content math.js
git commit -am "debug: more temp stuff"

# Rebase
git rebase -i HEAD~2

# Change to:
drop abc1234 debug: temp logging
drop def5678 debug: more temp stuff

# Or just delete the lines entirely

# Save. Debug commits are gone!
```

### Task 9: Edit a Commit (Split it)
Split one commit into two:

```powershell
"function sqrt(x) { return Math.sqrt(x); }
function pow(x, y) { return Math.pow(x, y); }" | Out-File advanced.js -Encoding UTF8
git add advanced.js
git commit -m "add advanced functions"

# Rebase
git rebase -i HEAD~1

# Change to:
edit abc1234 add advanced functions

# Save. Rebase stops at that commit.
# Now split it:
git reset HEAD~1  # Unstage the commit
git add advanced.js
git commit -m "feat: add sqrt function"
git commit -am "feat: add pow function"

# Continue rebase
git rebase --continue

# Check log - one commit became two!
```

### Task 10: Reword Multiple Messages
Make all messages follow conventional commits:

```powershell
git rebase -i HEAD~5

# Change multiple to reword:
reword abc1234 added feature x
reword def5678 fixed something
reword ghi9012 updated code
pick jkl3456 feat: good message
pick mno7890 fix: another good message

# Save. Editor opens for EACH reworded commit.
# Fix each one with proper format:
# "added feature x" → "feat: add feature x"
# "fixed something" → "fix: resolve calculation error"
# "updated code" → "refactor: improve code structure"
```

### Task 11: Exec Command
Run tests between commits:

```powershell
git rebase -i HEAD~3

# Add exec commands:
pick abc1234 feat: add function
exec npm test
pick def5678 fix: fix bug
exec npm test
pick ghi9012 docs: update README

# Save. Git will run tests after each commit!
# If test fails, rebase stops for you to fix.
```

### Task 12: Rebase with Conflicts
Handle conflicts during rebase:

```powershell
# Create conflict scenario
git checkout -b feature
"new feature" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "feat: add feature"

git checkout main
"main work" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "feat: different feature"

# Try to rebase
git checkout feature
git rebase main

# Conflict! Resolve it:
# 1. Open feature.txt
# 2. Fix conflicts
# 3. git add feature.txt
# 4. git rebase --continue

# Or abort: git rebase --abort
```

### Task 13: Autosquash Workflow
Git's smart squashing:

```powershell
"function test() {}" | Out-File test.js -Encoding UTF8
git add test.js
git commit -m "feat: add test function"

# Later, you find a bug in that commit
"function test() { return true; }" | Out-File test.js -Encoding UTF8
git commit -am "fixup! feat: add test function"

# Rebase with autosquash
git rebase -i --autosquash HEAD~2

# Git automatically marks the fixup!
# Just save and it squashes.
```

### Task 14: Rebase Onto Different Base
Change where branch started:

```powershell
# Create scenario
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

# Rebase feature onto v2 (not v3)
git checkout feature
git rebase --onto main~1 main

# Feature now based on v2!
```

### Task 15: Complete Cleanup Workflow
Full professional cleanup:

```powershell
# You have messy branch with 10 commits
# Goal: Clean to 3 logical commits

git checkout -b feature-cleanup

# View the mess
git log --oneline -10

# Interactive rebase
git rebase -i HEAD~10

# In editor, organize:
pick commit1 feat: start feature
squash commit2 WIP
squash commit3 more work
fixup commit4 typo
reword commit5 added tests
squash commit6 more tests
squash commit7 docs
pick commit8 fix: bug found in review
fixup commit9 oops
drop commit10 debug code

# Save and clean up messages
# Result: 3 clean commits:
# 1. feat: implement feature with tests
# 2. docs: add documentation
# 3. fix: resolve review feedback
```

## ✅ Expected Outcome

You should be able to:
- Clean up messy commit history
- Squash related commits
- Reword unprofessional messages
- Reorder commits logically
- Split large commits
- Remove debug/WIP commits

## 🎓 Key Concepts

### When to Use Interactive Rebase

**✅ Good Use Cases:**
- Cleaning up local commits before pushing
- Squashing "fix typo" commits
- Rewriting unprofessional messages
- Combining related commits
- Removing debug commits

**❌ Don't Use For:**
- Commits already pushed to shared remote
- Public branch history
- Commits others based work on

### Common Patterns

```powershell
# Squash all commits in branch
git rebase -i main

# Squash last N commits
git rebase -i HEAD~N

# Edit specific commit
git rebase -i <commit>^

# With autosquash
git commit --fixup=<hash>
git rebase -i --autosquash
```

## 🔍 Verification Commands

```powershell
# View commit history
git log --oneline

# Detailed log
git log --oneline --graph --all

# See what changed
git show HEAD

# Check if rebase in progress
git status
```

## 💡 Pro Tips

### Rebase Safely
```powershell
# Create backup branch first
git branch backup

# Do your rebase
git rebase -i HEAD~5

# If disaster happens:
git reset --hard backup
```

### Useful Git Config
```powershell
# Set editor for rebase
git config --global core.editor "code --wait"

# Enable autosquash by default
git config --global rebase.autosquash true

# Auto-stash before rebase
git config --global rebase.autoStash true
```

### Abort vs Continue
```powershell
# Abort rebase (undo everything)
git rebase --abort

# Continue after fixing conflicts
git rebase --continue

# Skip current commit
git rebase --skip
```

## 🎯 What You Should Know Now

- ✅ Interactive rebase commands (pick, squash, fixup, reword, edit, drop)
- ✅ How to clean up commit history
- ✅ Squashing vs fixup
- ✅ Reordering commits
- ✅ Splitting commits
- ✅ When NOT to rebase
- ✅ Handling rebase conflicts

## 📊 Interactive Rebase Cheatsheet

```powershell
# Start interactive rebase
git rebase -i HEAD~N
git rebase -i <commit>
git rebase -i main

# In the editor:
pick abc1234 commit message    # Use commit
reword abc1234 commit message  # Change message
edit abc1234 commit message    # Stop to amend
squash abc1234 commit message  # Merge, keep messages
fixup abc1234 commit message   # Merge, drop message
drop abc1234 commit message    # Remove commit
exec npm test                  # Run command

# During rebase:
git rebase --continue   # Continue after edit/conflict
git rebase --abort      # Cancel rebase
git rebase --skip       # Skip current commit

# Autosquash workflow:
git commit --fixup=<hash>
git rebase -i --autosquash HEAD~N
```

## 🧪 Practice Scenarios

### Scenario 1: Messy Feature Branch
```powershell
# You have:
- "WIP"
- "fix"
- "more fixes"
- "forgot this"
- "typo"

# Clean to:
- "feat: implement user authentication"
```

### Scenario 2: Multiple Bug Fixes
```powershell
# You have:
- "fix login bug"
- "fix login bug for real"
- "oops one more fix"

# Clean to:
- "fix: resolve login authentication issue"
```

### Scenario 3: Reorder for Logic
```powershell
# You have:
- docs update
- feature implementation
- tests
- more feature work

# Reorder to:
- feature implementation
- more feature work
- tests
- docs update
```

## ⏭️ Next Exercise
Move on to **Exercise 16: Git Bisect** for binary search debugging!

---

**Time to Complete**: 40 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: Exercise 01-14  
**⚠️ Remember**: Never rebase public history!
