# Exercise 04: Staging Area Mastery - The Index

## 🎯 Objective
Master the staging area (index) - Git's preparation zone between working directory and repository.

## 📚 Concepts Covered
- Selective staging with `git add`
- Partial staging with `git add -p`
- Unstaging with `git reset` and `git restore --staged`
- The purpose of the staging area
- Interactive staging
- Viewing staged vs unstaged changes

## 📝 Preparation

```powershell
mkdir staging-mastery
cd staging-mastery
git init

# Create initial files
"# Shopping Cart App" | Out-File README.md -Encoding UTF8
"const cart = [];" | Out-File cart.js -Encoding UTF8
"const user = {};" | Out-File user.js -Encoding UTF8
git add .
git commit -m "Initial commit: Shopping cart foundation"
```

## 📝 Tasks

### Task 1: Selective File Staging
1. Modify all three files:
   ```powershell
   "## Features" | Out-File -Append README.md -Encoding UTF8
   "function addToCart() {}" | Out-File -Append cart.js -Encoding UTF8
   "function login() {}" | Out-File -Append user.js -Encoding UTF8
   ```
2. Check status (all three modified)
3. Stage ONLY cart.js: `git add cart.js`
4. Check status (cart.js staged, others not)
5. View staged diff: `git diff --staged`
6. View unstaged diff: `git diff`
7. Commit only cart.js: `git commit -m "Add addToCart function"`
8. Check status (README and user.js still modified)

### Task 2: Stage Multiple Specific Files
1. Modify multiple files again
2. Stage multiple at once: `git add README.md user.js`
3. Verify both are staged
4. Commit both together

### Task 3: Stage All Modified Files
1. Modify all three files again
2. Stage all tracked files: `git add -u`
3. Check status (all staged)
4. Commit all

### Task 4: Stage New and Modified Files
1. Modify cart.js
2. Create new file: `"const product = {};" | Out-File product.js -Encoding UTF8`
3. Stage everything: `git add .`
4. Check status (both staged)
5. Understanding: `.` stages all (new + modified)

### Task 5: Unstaging Files with restore
1. Modify README.md and stage it
2. Change your mind - unstage: `git restore --staged README.md`
3. Check status (modified but not staged)
4. File content unchanged, just unstaged

### Task 6: Unstaging with reset
1. Stage a file: `git add cart.js`
2. Unstage with reset: `git reset HEAD cart.js`
3. Same result as restore --staged
4. Both methods work, restore is newer

### Task 7: Partial Staging (Interactive)
1. Make multiple changes to one file:
   ```powershell
   @"
   const cart = [];

   function addToCart(item) {
     cart.push(item);
   }

   function removeFromCart(item) {
     const index = cart.indexOf(item);
     cart.splice(index, 1);
   }

   function clearCart() {
     cart.length = 0;
   }
   "@ | Out-File cart.js -Encoding UTF8
   ```
2. Interactive staging: `git add -p cart.js`
3. Git shows each change (hunk) and asks: Stage this hunk? (y/n/q/a/d/?)
4. Choose 'y' for some, 'n' for others
5. Check status: same file in staged AND unstaged!
6. This stages PARTS of a file

### Task 8: Understanding Hunks
During `git add -p`, you see options:
```
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this or remaining hunks
a - stage this and all remaining hunks
d - do not stage this or remaining hunks
s - split current hunk into smaller hunks
e - manually edit the current hunk
? - print help
```

Practice using each option!

### Task 9: Stage All Except Certain Files
1. Modify multiple files
2. Stage all: `git add .`
3. Realize you don't want one: `git restore --staged unwanted.js`
4. Commit the rest

### Task 10: Viewing What's Staged
1. Stage some changes
2. View what's staged: `git diff --staged`
3. View what's not staged: `git diff`
4. View both: `git diff HEAD`
5. Understanding:
   - `git diff` = Working Directory ← → Staging Area
   - `git diff --staged` = Staging Area ← → Last Commit
   - `git diff HEAD` = Working Directory ← → Last Commit

### Task 11: Stage by Extension
1. Create multiple files:
   ```powershell
   "test" | Out-File test1.js -Encoding UTF8
   "test" | Out-File test2.js -Encoding UTF8
   "test" | Out-File test1.txt -Encoding UTF8
   "test" | Out-File test2.txt -Encoding UTF8
   ```
2. Stage only .js files: `git add *.js`
3. Check status (only .js files staged)
4. Commit them
5. Stage remaining: `git add *.txt`

### Task 12: Stage Directory
1. Create directory with files:
   ```powershell
   mkdir utils
   "export const helper = () => {};" | Out-File utils/helper.js -Encoding UTF8
   "export const validator = () => {};" | Out-File utils/validator.js -Encoding UTF8
   ```
2. Stage entire directory: `git add utils/`
3. All files in directory are staged
4. Commit

### Task 13: The Staging Area Purpose
Create this scenario to understand WHY staging exists:

1. Make 3 unrelated changes:
   - Add feature A to cart.js
   - Add feature B to user.js
   - Fix bug in product.js
2. Stage only feature A: `git add cart.js`
3. Commit: "feat: Add feature A"
4. Stage only feature B: `git add user.js`
5. Commit: "feat: Add feature B"
6. Stage bug fix: `git add product.js`
7. Commit: "fix: Bug in product"

Result: 3 atomic commits instead of one messy commit!

### Task 14: Staging Area with Merge Conflicts
1. Create a branch and make conflicting changes
2. Merge - conflict occurs
3. Resolve conflict in file
4. Stage resolved file: `git add conflicted-file.js`
5. This marks conflict as resolved
6. Complete merge commit

### Task 15: Reset Staging Area
1. Stage multiple files
2. Realize you want to start over
3. Unstage all: `git reset`
4. All files back to unstaged
5. Re-stage correctly

## ✅ Expected Outcome

You should understand:
- Purpose of staging area (creating atomic commits)
- Multiple ways to stage files
- Selective staging with -p
- How to unstage files
- Difference between staged and unstaged
- When and why to use staging area

## 🎓 Key Concepts

### The Three Trees (Revisited)
```
Working Directory  →  Staging Area  →  Repository
   (modified)       (git add)        (git commit)
```

### Why Staging Exists
- **Atomic commits**: Commit related changes together
- **Review before commit**: Check what you're committing
- **Selective commits**: Commit parts of files
- **Quality control**: Stage only what's ready

### Staging vs Committing
```
Stage: Prepare changes for commit (git add)
Commit: Save snapshot to history (git commit)

Can stage many times before committing
Can unstage and restage differently
Commit is permanent, staging is temporary
```

## 🔍 Verification Commands

```powershell
# See staged changes
git diff --staged

# See unstaged changes
git diff

# See all changes (staged + unstaged)
git diff HEAD

# List staged files
git diff --staged --name-only

# Check status
git status
```

## 💡 Pro Tips

### Best Practices
```powershell
# Review before staging
git diff

# Review before committing
git diff --staged

# Use interactive staging for complex changes
git add -p

# Stage logically related changes together
```

### Quick Commands
```powershell
# Stage all tracked files
git add -u

# Stage everything (new + modified)
git add .

# Unstage everything
git reset

# Unstage specific file
git restore --staged file
```

### Workflow
```
1. Make changes
2. Review: git diff
3. Stage selectively: git add -p
4. Review staged: git diff --staged
5. Commit: git commit -m "message"
6. Repeat for remaining changes
```

## 🎯 What You Should Know Now

- ✅ Purpose of the staging area
- ✅ Multiple ways to stage files
- ✅ How to unstage files
- ✅ Partial staging with -p
- ✅ Viewing staged vs unstaged changes
- ✅ Creating atomic commits
- ✅ When to use different staging methods

## 📊 Staging Commands Cheatsheet

```powershell
# Stage specific files
git add file1.js file2.js

# Stage all changes
git add .

# Stage all tracked files only
git add -u

# Stage by pattern
git add *.js

# Stage directory
git add folder/

# Interactive staging
git add -p

# Unstage (new way)
git restore --staged file

# Unstage (old way)
git reset HEAD file

# Unstage all
git reset

# View staged
git diff --staged

# View unstaged
git diff
```

## ⏭️ Next Exercise
Move on to **Exercise 05: Commit Messages & Amending** to learn professional commit practices!

---

**Time to Complete**: 25 minutes  
**Difficulty**: ⭐ Beginner  
**Prerequisites**: Exercise 01-03
