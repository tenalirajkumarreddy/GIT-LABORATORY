# Exercise 14: Rebasing - Rewriting History

## 🎯 Objective
Master git rebase to create linear history, update feature branches, and understand when to rebase vs merge.

## 📚 Concepts Covered
- Basic rebasing
- Rebasing vs merging
- Interactive rebase
- Rebase conflicts
- The golden rule of rebasing
- Rebase onto
- Abort and continue rebase

## ⚠️ CRITICAL: The Golden Rule
**NEVER rebase commits that have been pushed to a shared/public branch!**

## 📝 Preparation

```powershell
mkdir rebase-mastery
cd rebase-mastery
git init

# Create main branch history
"v1" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Version 1"

"v2" | Out-File app.js -Encoding UTF8
git commit -am "Version 2"

"v3" | Out-File app.js -Encoding UTF8
git commit -am "Version 3"
```

## 📝 Tasks

### Task 1: Basic Rebase
1. Create and switch to feature branch: `git checkout -b feature/add-auth`
2. Make 2 commits:
   ```powershell
   "auth v1" | Out-File auth.js -Encoding UTF8
   git add auth.js
   git commit -m "Add auth v1"
   
   "auth v2" | Out-File auth.js -Encoding UTF8
   git commit -am "Add auth v2"
   ```
3. Switch to main: `git checkout main`
4. Make 2 commits on main:
   ```powershell
   "v4" | Out-File app.js -Encoding UTF8
   git commit -am "Version 4"
   
   "v5" | Out-File app.js -Encoding UTF8
   git commit -am "Version 5"
   ```
5. View graph: `git log --oneline --graph --all`
6. Switch to feature: `git checkout feature/add-auth`
7. Rebase onto main: `git rebase main`
8. View graph again - feature branch commits are now ON TOP of main!

### Task 2: Understanding Rebase vs Merge

**Before Rebase:**
```
main:    C1 - C2 - C3 - C4 - C5
                \
feature:         F1 - F2
```

**After Rebase:**
```
main:    C1 - C2 - C3 - C4 - C5
                              \
feature:                       F1' - F2'
```

**After Merge:**
```
main:    C1 - C2 - C3 - C4 - C5 - M
                \                /
feature:         F1 -------- F2
```

1. Create another branch: `git checkout -b feature/test-merge main`
2. Make commits on it
3. Merge it into main: `git checkout main; git merge feature/test-merge`
4. View graph - see merge commit
5. Compare with rebased feature branch

### Task 3: Rebase with Conflicts
1. Create branch: `git checkout -b feature/conflicting main`
2. Modify app.js: Change line 1 to "FEATURE VERSION"
3. Commit: "Feature changes"
4. Checkout main: `git checkout main`
5. Modify app.js: Change line 1 to "MAIN VERSION"
6. Commit: "Main changes"
7. Checkout feature: `git checkout feature/conflicting`
8. Rebase: `git rebase main`
9. CONFLICT! 🔥
10. Open app.js, resolve conflict
11. Stage resolved file: `git add app.js`
12. Continue rebase: `git rebase --continue`
13. Rebase completes!

### Task 4: Abort a Rebase
1. Start a rebase that conflicts
2. Realize it's too complex
3. Abort: `git rebase --abort`
4. Back to pre-rebase state
5. Branch still exists, no changes made

### Task 5: Interactive Rebase - Reorder Commits
1. Create branch with 4 commits:
   ```powershell
   git checkout -b feature/interactive main
   "step1" | Out-File step1.js -Encoding UTF8; git add step1.js; git commit -m "Step 1"
   "step2" | Out-File step2.js -Encoding UTF8; git add step2.js; git commit -m "Step 2"
   "step3" | Out-File step3.js -Encoding UTF8; git add step3.js; git commit -m "Step 3"
   "step4" | Out-File step4.js -Encoding UTF8; git add step4.js; git commit -m "Step 4"
   ```
2. Interactive rebase last 4 commits: `git rebase -i HEAD~4`
3. Editor opens showing:
   ```
   pick abc1234 Step 1
   pick def5678 Step 2
   pick ghi9012 Step 3
   pick jkl3456 Step 4
   ```
4. Reorder lines (e.g., swap Step 2 and Step 3)
5. Save and close editor
6. View log - commits are reordered!

### Task 6: Interactive Rebase - Squash Commits
1. Create branch with messy history:
   ```powershell
   git checkout -b feature/squash main
   "code" | Out-File code.js -Encoding UTF8; git add code.js; git commit -m "Add code"
   "fix typo" | Out-File code.js -Encoding UTF8; git commit -am "fix typo"
   "fix another" | Out-File code.js -Encoding UTF8; git commit -am "fix another"
   "final fix" | Out-File code.js -Encoding UTF8; git commit -am "final fix"
   ```
2. Interactive rebase: `git rebase -i HEAD~4`
3. Change to:
   ```
   pick abc1234 Add code
   squash def5678 fix typo
   squash ghi9012 fix another
   squash jkl3456 final fix
   ```
4. Save, editor opens for combined message
5. Write clean message: "Add code feature"
6. View log - 4 commits became 1!

### Task 7: Interactive Rebase - Edit Commits
1. Create commits with mistakes
2. Interactive rebase: `git rebase -i HEAD~3`
3. Change `pick` to `edit` for commit to modify
4. Save editor
5. Git stops at that commit
6. Make changes to files
7. Stage: `git add .`
8. Amend: `git commit --amend`
9. Continue: `git rebase --continue`

### Task 8: Interactive Rebase - Reword Messages
1. Create commits with bad messages
2. Interactive rebase: `git rebase -i HEAD~3`
3. Change `pick` to `reword` for commits
4. Save
5. Editor opens for each message
6. Rewrite with better messages
7. View log - clean messages!

### Task 9: Interactive Rebase - Drop Commits
1. Create several commits
2. Realize one commit should be removed
3. Interactive rebase: `git rebase -i HEAD~5`
4. Change `pick` to `drop` for unwanted commit
5. Or just delete that line
6. Save
7. Commit is gone from history

### Task 10: Fixup (Squash without Message)
1. Create commits:
   ```powershell
   "feature" | Out-File feature.js -Encoding UTF8; git add feature.js; git commit -m "Add feature"
   "oops" | Out-File feature.js -Encoding UTF8; git commit -am "oops forgot this"
   ```
2. Interactive rebase: `git rebase -i HEAD~2`
3. Use `fixup` instead of `squash`:
   ```
   pick abc1234 Add feature
   fixup def5678 oops forgot this
   ```
4. Saves without opening message editor
5. Second commit's message is discarded

### Task 11: Rebase --onto (Advanced)
1. Create branch structure:
   ```powershell
   git checkout main
   git checkout -b feature-base
   "base1" | Out-File base.js -Encoding UTF8; git add base.js; git commit -m "Base 1"
   git checkout -b feature-derived
   "derived1" | Out-File derived.js -Encoding UTF8; git add derived.js; git commit -m "Derived 1"
   "derived2" | Out-File derived.js -Encoding UTF8; git commit -am "Derived 2"
   ```
2. Realize feature-derived should be based on main, not feature-base
3. Rebase onto: `git rebase --onto main feature-base feature-derived`
4. feature-derived now branches from main!

### Task 12: Update Feature Branch (Common Workflow)
1. You're working on long-lived feature branch
2. Main has new commits
3. Update feature with main changes:
   ```powershell
   git checkout feature-branch
   git fetch origin
   git rebase origin/main
   ```
4. Resolve any conflicts
5. Force push if already pushed: `git push --force-with-lease`

### Task 13: Rebase vs Merge Decision Practice

Create scenarios and decide:

**Scenario A: Feature branch, not pushed**
- Action: Rebase ✅ (clean history)

**Scenario B: Feature branch, pushed to origin**
- Action: Depends - if others use it, merge. If just you, rebase.

**Scenario C: Main branch with new commits**
- Action: Merge into main ✅ (never rebase main)

**Scenario D: Cleaning up local commits before pushing**
- Action: Interactive rebase ✅ (squash/reorder)

### Task 14: Autosquash (Pro Technique)
1. Make a commit: `git commit -m "Add feature"`
2. Realize you need to fix it
3. Make fix commit: `git commit -am "fixup! Add feature"`
4. Interactive rebase with autosquash: `git rebase -i --autosquash HEAD~2`
5. Git automatically marks fixup commits!

### Task 15: Rebase Preserve Merges
1. Create branch with merged history
2. Rebase with: `git rebase --rebase-merges main`
3. Preserves merge structure during rebase
4. Useful for complex branch structures

## ✅ Expected Outcome

You should know:
- How to rebase branches
- Difference between rebase and merge
- Interactive rebase operations
- When it's safe vs dangerous to rebase
- How to handle rebase conflicts
- Real-world rebase workflows

## 🎓 Key Concepts

### Interactive Rebase Commands
```
pick   = use commit
reword = use commit, edit message
edit   = use commit, stop to amend
squash = use commit, meld into previous
fixup  = like squash, discard message
exec   = run command
break  = stop here (continue with 'git rebase --continue')
drop   = remove commit
```

### When to Rebase vs Merge

**Use Rebase:**
- ✅ Local feature branch
- ✅ Haven't pushed yet
- ✅ Want linear history
- ✅ Clean up before pushing

**Use Merge:**
- ✅ Main/shared branches
- ✅ Already pushed to public repo
- ✅ Want to preserve history
- ✅ Collaborative branches

## 🔍 Verification Commands

```powershell
# View linear history
git log --oneline --graph

# Check rebase in progress
git status

# See what would be rebased
git log --oneline main..feature-branch

# Verify branch point
git merge-base main feature-branch
```

## ⚠️ The Golden Rule (Again!)

```
NEVER REBASE PUBLIC COMMITS!

✅ Local commits → Rebase OK
✅ Feature branch (only you) → Rebase OK
❌ Pushed to main → NEVER REBASE
❌ Others pulled your commits → NEVER REBASE
```

## 💡 Pro Tips

### Clean Up Before PR
```powershell
# Squash all commits into one
git rebase -i HEAD~10
# Mark all but first as squash

# Or reset and recommit
git reset --soft HEAD~10
git commit -m "Complete feature: description"
```

### Safe Force Push
```powershell
# Use --force-with-lease instead of --force
git push --force-with-lease

# This fails if remote has commits you don't have
# Prevents overwriting others' work
```

### Rebase Workflow
```powershell
# 1. Update main
git checkout main
git pull

# 2. Rebase feature
git checkout feature-branch
git rebase main

# 3. Resolve conflicts if any
# (resolve, git add, git rebase --continue)

# 4. Force push if needed
git push --force-with-lease
```

## 🎯 What You Should Know Now

- ✅ Basic rebasing workflow
- ✅ Interactive rebase operations
- ✅ Rebase vs merge trade-offs
- ✅ When rebasing is dangerous
- ✅ How to handle rebase conflicts
- ✅ Advanced rebase techniques
- ✅ Real-world rebase patterns

## 📊 Rebase Commands Cheatsheet

```powershell
# Basic rebase
git rebase main                   # Rebase onto main
git rebase --continue             # Continue after conflict
git rebase --abort                # Cancel rebase
git rebase --skip                 # Skip problematic commit

# Interactive rebase
git rebase -i HEAD~3              # Last 3 commits
git rebase -i main                # All commits since main
git rebase -i --autosquash HEAD~5 # Auto-mark fixup commits

# Advanced
git rebase --onto new-base old-base branch
git rebase --rebase-merges main   # Preserve merges
git rebase --exec "npm test" main # Run tests between each

# After rebase
git push --force-with-lease       # Safe force push
```

## ⏭️ Next Exercise
Ready for **Exercise 15: Interactive Rebase Mastery** - advanced history editing!

---

**Time to Complete**: 45 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: Exercise 01-13
