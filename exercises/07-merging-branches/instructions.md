# Exercise 07: Merging Branches - Combining Work

## 🎯 Objective
Master branch merging including fast-forward merges, three-way merges, and understanding merge commits.

## 📚 Concepts Covered
- Fast-forward merges
- Three-way merges
- Merge commits
- No-fast-forward merges
- Viewing merge history
- Aborting merges

## 📝 Preparation

```powershell
mkdir merging-practice
cd merging-practice
git init

# Create initial structure
"# Project Management Tool" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "Initial commit"

"const version = '1.0.0';" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Add app version"
```

## 📝 Tasks

### Task 1: Fast-Forward Merge
1. Create and switch to branch `feature/login`
2. Create `login.js`: `function login() { return true; }`
3. Commit: "Add login feature"
4. Create `logout.js`: `function logout() { return true; }`
5. Commit: "Add logout feature"
6. Switch back to main
7. Merge feature/login into main: `git merge feature/login`
8. Notice the "Fast-forward" message
9. View log graph - main simply moved forward
10. Delete feature/login branch (it's merged)

### Task 2: Understanding Fast-Forward
1. View log: `git log --oneline --graph`
2. Notice there's NO merge commit
3. Main just moved its pointer forward
4. This is because main hadn't diverged from feature/login

```
Before merge:
main        → [C2]
feature     → [C2] → [C3] → [C4]

After fast-forward:
main        → [C2] → [C3] → [C4]
```

### Task 3: Three-Way Merge (Diverged Branches)
1. Create and switch to `feature/dashboard`
2. Create `dashboard.js`: `const widgets = [];`
3. Commit: "Add dashboard widgets"
4. Switch back to main
5. Modify `app.js`, add: `const author = 'YourName';`
6. Commit: "Add author info"
7. Now main has diverged from feature/dashboard!
8. Merge feature/dashboard: `git merge feature/dashboard`
9. A merge commit is created (editor may open for message)
10. View log graph - see the merge commit connecting branches

### Task 4: Understanding Three-Way Merge
1. View detailed log: `git log --oneline --graph --all`
2. You should see a merge commit with two parents
3. This happens when branches have diverged

```
Before merge:
        feature → [C6]
       ↗
main → [C5]

After three-way merge:
main → [C5] → [M7]
         ↖   ↗
          [C6]
```

### Task 5: No-Fast-Forward Merge
1. Create branch `feature/settings`
2. Switch to it
3. Create `settings.js`: `const settings = {};`
4. Commit: "Add settings"
5. Switch to main
6. Merge with `--no-ff` flag: `git merge --no-ff feature/settings`
7. This creates a merge commit even though fast-forward was possible
8. View log graph - see explicit merge commit
9. This preserves branch history

### Task 6: Merge Message Customization
1. Create branch `feature/notifications`
2. Create `notifications.js` and commit
3. Switch to main
4. Merge with custom message:
   `git merge feature/notifications -m "Merge notifications feature - adds alert system"`
5. View log to see your custom message

### Task 7: Viewing Merge Information
1. View all merges: `git log --merges`
2. View non-merge commits: `git log --no-merges`
3. Show first parent only: `git log --first-parent`
4. Show detailed merge commit: `git show HEAD` (if HEAD is a merge)

### Task 8: Merge Statistics
1. Create branch `feature/reports`
2. Create multiple files and commits
3. Switch to main
4. Merge with stat: `git merge --stat feature/reports`
5. See files changed, insertions, deletions
6. Alternatively: `git merge feature/reports` then `git show --stat`

### Task 9: Dry Run Merge
1. Create branch `feature/export`
2. Make some commits
3. Switch to main
4. Check if merge would succeed: `git merge --no-commit --no-ff feature/export`
5. Review the result in staging
6. If happy, commit to complete
7. If not, abort: `git merge --abort`

### Task 10: Practice Merge Workflow
1. Create branch `feature/import`
2. Make 3 commits with actual code changes
3. Switch to main and make 2 commits
4. Merge feature/import
5. Delete the feature branch
6. View final history graph

## ✅ Expected Outcome

Your repository should have:
- Multiple merge commits
- Clear branch history in log graph
- Understanding of when fast-forward happens vs three-way merge
- Clean main branch with all features merged

## 🎓 Key Concepts

### Fast-Forward Merge
- Happens when target branch hasn't diverged
- No merge commit created
- Branch pointer simply moves forward
- Linear history maintained

### Three-Way Merge
- Happens when branches have diverged
- Creates a merge commit with two parents
- Combines changes from both branches
- Non-linear history

### Merge Commit
- Special commit with multiple parents
- Represents the joining of branches
- Contains combined changes

## 🔍 Verification Commands

```powershell
# View all merges
git log --merges --oneline

# View graph
git log --oneline --graph --all

# Show merge commits
git log --merges

# Show non-merge commits
git log --no-merges

# See what branches are merged
git branch --merged
```

## 💡 Pro Tips

### When to Use --no-ff
```powershell
# Preserve feature branch history
git merge --no-ff feature/user-auth

# Good for:
# - Feature branches in gitflow
# - Keeping clear feature boundaries
# - Easier to revert entire feature
```

### Merge vs Rebase (Preview)
```
Merge:  Preserves history, creates merge commits
Rebase: Linear history, no merge commits (next exercise!)
```

### Best Practices
1. **Always be on the target branch**: Switch to main before merging feature
2. **Pull before merge**: Get latest changes first
3. **Delete merged branches**: Keep branch list clean
4. **Use descriptive merge messages**: Especially for important features

## 🎯 What You Should Know Now

- ✅ Difference between fast-forward and three-way merge
- ✅ When each type of merge occurs
- ✅ How to create merge commits
- ✅ How to prevent fast-forward with --no-ff
- ✅ How to view merge history
- ✅ How to abort a merge
- ✅ Best practices for merging

## 📊 Merge Decision Tree

```
Are you on the target branch?
├─ No → Switch to target branch first
└─ Yes
    ↓
Has target branch diverged from feature?
├─ No → Fast-forward merge (or use --no-ff)
└─ Yes → Three-way merge will occur
    ↓
Any conflicts?
├─ Yes → Resolve conflicts (next exercise!)
└─ No → Merge completes automatically
```

## ⏭️ Next Exercise
Ready for **Exercise 08: Merge Conflicts** - the real challenge!

---

**Time to Complete**: 30 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-06
