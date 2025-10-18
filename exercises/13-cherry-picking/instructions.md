# Exercise 13: Cherry-Picking - Selecting Specific Commits

## 🎯 Objective
Master git cherry-pick to selectively apply commits from one branch to another without merging entire branches.

## 📚 Concepts Covered
- Basic cherry-picking
- Cherry-pick multiple commits
- Cherry-pick with conflicts
- Cherry-pick without committing
- Edit commits while cherry-picking
- Cherry-pick from different branches
- Aborting cherry-picks

## 📝 Preparation

```powershell
mkdir cherry-pick-practice
cd cherry-pick-practice
git init

# Create main branch
"v1.0" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Version 1.0"

"v1.1" | Out-File app.js -Encoding UTF8
git commit -am "Version 1.1"
```

## 📝 Tasks

### Task 1: Basic Cherry-Pick
1. Create feature branch:
   ```powershell
   git checkout -b feature/experimental
   "Feature A" | Out-File featureA.js -Encoding UTF8
   git add featureA.js
   git commit -m "Add Feature A"
   
   "Feature B" | Out-File featureB.js -Encoding UTF8
   git add featureB.js
   git commit -m "Add Feature B"
   
   "Feature C" | Out-File featureC.js -Encoding UTF8
   git add featureC.js
   git commit -m "Add Feature C"
   ```
2. View log and copy hash of "Add Feature B" commit
3. Switch to main: `git checkout main`
4. Cherry-pick Feature B only: `git cherry-pick <hash>`
5. View log - Feature B is now on main!
6. Check files - only featureB.js exists, not A or C

### Task 2: Understanding Cherry-Pick

**Before:**
```
main:        C1 - C2
feature:     C1 - C2 - FA - FB - FC
```

**After cherry-picking FB:**
```
main:        C1 - C2 - FB'
feature:     C1 - C2 - FA - FB - FC
```

Note: FB' is a NEW commit (different hash than FB)

### Task 3: Cherry-Pick Multiple Commits
1. View feature branch log: `git log feature/experimental --oneline`
2. Note hashes of Feature A and Feature C
3. On main, cherry-pick both: `git cherry-pick <hash-A> <hash-C>`
4. Both commits are applied in order
5. Check files - now have A, B, and C on main

### Task 4: Cherry-Pick Range of Commits
1. Create new branch with multiple commits:
   ```powershell
   git checkout -b hotfix
   "fix1" | Out-File fix1.js -Encoding UTF8; git add fix1.js; git commit -m "Fix 1"
   "fix2" | Out-File fix2.js -Encoding UTF8; git add fix2.js; git commit -m "Fix 2"
   "fix3" | Out-File fix3.js -Encoding UTF8; git add fix3.js; git commit -m "Fix 3"
   "fix4" | Out-File fix4.js -Encoding UTF8; git add fix4.js; git commit -m "Fix 4"
   ```
2. Note hash of Fix 2 and Fix 4
3. Switch to main: `git checkout main`
4. Cherry-pick range: `git cherry-pick <hash-fix2>..<hash-fix4>`
5. This picks Fix 3 and Fix 4 (exclusive start, inclusive end)
6. To include start: `git cherry-pick <hash-fix2>^..<hash-fix4>`

### Task 5: Cherry-Pick with Conflicts
1. Create conflicting scenario:
   ```powershell
   git checkout -b conflict-branch
   "Conflict line" | Out-File app.js -Encoding UTF8
   git commit -am "Conflict commit"
   ```
2. Get commit hash
3. Switch to main: `git checkout main`
4. Modify app.js differently:
   ```powershell
   "Different line" | Out-File app.js -Encoding UTF8
   git commit -am "Different change"
   ```
5. Try cherry-pick: `git cherry-pick <hash>`
6. CONFLICT! 🔥
7. Open app.js, resolve conflict
8. Stage: `git add app.js`
9. Continue: `git cherry-pick --continue`

### Task 6: Cherry-Pick Without Committing
1. Create commit on feature branch
2. Switch to main
3. Cherry-pick without auto-commit: `git cherry-pick -n <hash>`
4. Changes are staged but not committed
5. You can modify them
6. Then commit manually: `git commit -m "Modified cherry-pick"`

### Task 7: Cherry-Pick and Edit
1. Cherry-pick with edit option: `git cherry-pick -e <hash>`
2. Editor opens for commit message
3. Modify the message
4. Save and close
5. Commit is applied with new message

### Task 8: Cherry-Pick Multiple from Different Branches
1. Create two feature branches with commits
2. On main, cherry-pick from both:
   ```powershell
   git cherry-pick feature1~2  # 3rd commit from feature1
   git cherry-pick feature2~1  # 2nd commit from feature2
   ```
3. Commits from different branches now on main

### Task 9: Abort Cherry-Pick
1. Start a cherry-pick that conflicts
2. Decide it's too complex
3. Abort: `git cherry-pick --abort`
4. Returns to pre-cherry-pick state

### Task 10: Cherry-Pick with Signoff
1. Cherry-pick and add signoff: `git cherry-pick -s <hash>`
2. View commit: `git show`
3. See "Signed-off-by" line added
4. Useful for maintaining attribution

### Task 11: Cherry-Pick and Track Original
1. Cherry-pick: `git cherry-pick -x <hash>`
2. View commit message: `git show`
3. See "(cherry picked from commit ...)" reference
4. Maintains traceability

### Task 12: Real-World Scenario - Hotfix to Multiple Versions
1. Create version branches:
   ```powershell
   git checkout -b version-1.0 main
   "v1.0 release" | Out-File version.txt -Encoding UTF8
   git add version.txt
   git commit -m "v1.0 release"
   
   git checkout -b version-2.0 main
   "v2.0 release" | Out-File version.txt -Encoding UTF8
   git add version.txt
   git commit -m "v2.0 release"
   ```
2. Create hotfix on main:
   ```powershell
   git checkout main
   "Security fix" | Out-File security.js -Encoding UTF8
   git add security.js
   git commit -m "SECURITY: Fix critical vulnerability"
   ```
3. Get hotfix hash
4. Apply to v1.0: `git checkout version-1.0; git cherry-pick <hash>`
5. Apply to v2.0: `git checkout version-2.0; git cherry-pick <hash>`
6. All versions now have the fix!

### Task 13: Cherry-Pick Only Certain Files
1. Cherry-pick commit: `git cherry-pick -n <hash>`
2. Unstage files you don't want: `git restore --staged unwanted.js`
3. Delete unwanted changes: `git restore unwanted.js`
4. Commit only wanted changes: `git commit`

### Task 14: Interactive Cherry-Pick (via Rebase)
1. Want to cherry-pick but reorder/edit
2. Use interactive rebase instead:
   ```powershell
   git rebase -i --onto main <start-hash> <end-hash>
   ```
3. Pick only commits you want
4. Reorder/squash as needed

### Task 15: Cherry-Pick vs Merge Decision

Practice deciding which to use:

**Use Cherry-Pick When:**
- Need ONE specific commit
- Don't want entire branch
- Applying hotfix to multiple versions
- Commit is on wrong branch

**Use Merge When:**
- Want ALL commits from branch
- Maintaining branch history
- Standard workflow
- Collaborative development

## ✅ Expected Outcome

You should know:
- How to cherry-pick single and multiple commits
- How to handle cherry-pick conflicts
- When to cherry-pick vs merge
- Real-world cherry-pick scenarios
- Advanced cherry-pick options

## 🎓 Key Concepts

### What is Cherry-Pick?
Cherry-pick creates a NEW commit with the same changes as the original. It's NOT moving a commit - it's copying changes.

### Cherry-Pick Creates New Commits
```
Original:  abc123 "Add feature"
After:     def456 "Add feature"  (different hash!)
```

### Cherry-Pick vs Merge
```
Cherry-Pick: Copy specific commits
Merge:       Combine entire branch histories
```

## 🔍 Verification Commands

```powershell
# Check cherry-pick in progress
git status

# See commit history
git log --oneline --graph --all

# Compare original and cherry-picked commit
git show <original-hash>
git show <cherry-picked-hash>

# See if commit exists on branch
git branch --contains <hash>
```

## 💡 Pro Tips

### Finding Commit to Cherry-Pick
```powershell
# View commits on feature not on main
git log main..feature --oneline

# Find commit by message
git log --all --grep="pattern"

# Find commit that changed file
git log --all -- filename
```

### Cherry-Pick from Another Repo
```powershell
# Add remote
git remote add other-repo <url>
git fetch other-repo

# Cherry-pick from it
git cherry-pick other-repo/branch~3
```

### Batch Cherry-Pick
```powershell
# Cherry-pick all commits touching a file
git log main..feature --oneline -- file.js | \
  cut -d' ' -f1 | \
  xargs git cherry-pick
```

## 🎯 What You Should Know Now

- ✅ How to cherry-pick commits
- ✅ Cherry-pick multiple commits
- ✅ Handle cherry-pick conflicts
- ✅ Cherry-pick without committing
- ✅ When to use cherry-pick vs merge
- ✅ Real-world cherry-pick scenarios
- ✅ Advanced cherry-pick techniques

## 📊 Cherry-Pick Commands Cheatsheet

```powershell
# Basic cherry-pick
git cherry-pick <hash>              # Pick one commit
git cherry-pick <hash1> <hash2>     # Pick multiple
git cherry-pick <start>..<end>      # Pick range

# Options
git cherry-pick -n <hash>           # Don't commit
git cherry-pick -e <hash>           # Edit message
git cherry-pick -x <hash>           # Add reference
git cherry-pick -s <hash>           # Add signoff

# Conflict handling
git cherry-pick --continue          # Continue after resolve
git cherry-pick --abort             # Cancel operation
git cherry-pick --skip              # Skip this commit

# Advanced
git cherry-pick --strategy=<strategy> <hash>
git cherry-pick -m 1 <merge-hash>   # Pick from merge parent 1
```

## 🐛 Common Mistakes

1. **Cherry-picking merge commits**: Use `-m` to specify parent
2. **Not testing after cherry-pick**: Changes might not work in new context
3. **Cherry-picking too many commits**: Consider merge instead
4. **Forgetting commits are duplicated**: Same changes, different hash
5. **Cherry-picking public commits**: Can confuse history

## 💼 Real-World Use Cases

### Use Case 1: Feature Toggle
```powershell
# Feature complete but not ready to merge all
# Pick only completed parts
git cherry-pick feature~5  # Specific ready commit
```

### Use Case 2: Backporting Bug Fixes
```powershell
# Fix in main, backport to stable
git checkout stable-v1
git cherry-pick main~2  # The bug fix
```

### Use Case 3: Wrong Branch Commit
```powershell
# Committed to main instead of feature
git checkout feature
git cherry-pick main  # Get the commit
git checkout main
git reset --hard HEAD~1  # Remove from main
```

## ⏭️ Next Exercise
Ready for **Exercise 14: Rebasing** - rewriting history cleanly!

---

**Time to Complete**: 35 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: Exercise 01-12
