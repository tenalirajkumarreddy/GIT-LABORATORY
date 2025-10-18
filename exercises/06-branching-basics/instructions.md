# Exercise 06: Branching Basics - Git's Superpower

## 🎯 Objective
Master Git branching - creating, switching, and understanding how branches work as lightweight movable pointers.

## 📚 Concepts Covered
- Creating branches
- Switching between branches
- Understanding HEAD pointer
- Branch listing and information
- Deleting branches
- Renaming branches

## 📝 Preparation

```powershell
mkdir branching-basics
cd branching-basics
git init

# Create initial commit
"# E-commerce Platform" | Out-File README.md -Encoding UTF8
"const express = require('express');" | Out-File server.js -Encoding UTF8
git add .
git commit -m "Initial commit: Setup express server"

# Add more commits to main
"const PORT = 3000;" | Out-File -Append server.js -Encoding UTF8
git add server.js
git commit -m "Add port configuration"

"module.exports = { PORT };" | Out-File config.js -Encoding UTF8
git add config.js
git commit -m "Add configuration module"
```

## 📝 Tasks

### Task 1: Create Your First Branch
1. List all existing branches (should show only main/master)
2. Create a new branch called `feature/user-auth`
3. List branches again (notice the * shows current branch)
4. Check git status (still on main)
5. View the log (both branches point to same commit)

### Task 2: Switch Branches
1. Switch to `feature/user-auth` using `git checkout`
2. Check status (should say "On branch feature/user-auth")
3. List branches (notice * moved)
4. View log (HEAD now points to feature/user-auth)

### Task 3: Make Changes on Feature Branch
1. While on `feature/user-auth`, create `auth.js`:
   ```javascript
   function login(username, password) {
     return true;
   }
   ```
2. Stage and commit: "feat: Add login function"
3. Modify `auth.js`, add logout function:
   ```javascript
   function logout() {
     return true;
   }
   ```
4. Stage and commit: "feat: Add logout function"
5. View log graph: `git log --oneline --graph --all`
6. Notice how feature/user-auth is ahead of main

### Task 4: Switch Back to Main
1. Switch back to main branch
2. List files (auth.js doesn't exist here!)
3. View log (commits from feature branch aren't here)
4. This demonstrates branches are independent timelines

### Task 5: Create and Switch in One Command
1. Create a new branch `feature/shopping-cart` and switch to it in one command
2. Create `cart.js`: `const cart = [];`
3. Commit: "feat: Initialize shopping cart"
4. View graph log (now you have 3 branches!)

### Task 6: Branch Management
1. Switch back to main
2. Create another branch `feature/payment`
3. Realize you made a typo, rename it to `feature/payment-gateway`
4. List all branches
5. Switch to `feature/shopping-cart`
6. Delete the `feature/payment-gateway` branch (should fail - not merged)
7. Force delete it using `git branch -D`

### Task 7: Understanding Branch Pointers
1. Switch to main
2. View log with decorations: `git log --oneline --all --decorate --graph`
3. Create a commit on main: modify README.md, add "## Branches"
4. Commit: "docs: Document branching strategy"
5. View graph again - see how main moved forward
6. Notice feature branches are still at their old positions

### Task 8: Branch Information
1. List all branches with their last commit: `git branch -v`
2. List branches with tracking information: `git branch -vv`
3. Show all branches (including remotes if any): `git branch -a`
4. Show merged branches: `git branch --merged`
5. Show unmerged branches: `git branch --no-merged`

### Task 9: Switch vs Checkout
1. Try the newer `git switch` command:
   - `git switch feature/user-auth`
2. Try creating and switching with switch:
   - `git switch -c feature/notifications`
3. Compare with checkout:
   - `git checkout -b feature/search`
4. Both work, but `switch` is more explicit

### Task 10: Working Directory and Branch Switching
1. Switch to main
2. Modify server.js (add a comment)
3. DON'T commit
4. Try to switch to feature/user-auth
5. Git will either:
   - Allow it (if no conflicts) and carry changes
   - Block it (if conflicts exist)
6. Understand you should commit or stash before switching

## ✅ Expected Outcome

Your repository should have:
- Main branch with 4 commits
- feature/user-auth with 2 additional commits
- feature/shopping-cart with 1 commit
- feature/notifications with 0 new commits
- feature/search with 0 new commits
- Understanding of branch as movable pointer

## 🎓 Key Concepts

### What is a Branch?
A branch is just a **pointer to a commit**. That's it!

```
main           → [C3]
                   ↑
feature/auth   → [C5] → [C6]
```

### What is HEAD?
HEAD is a pointer to the current branch you're on.

```
HEAD → main → [C3]
```

When you switch branches:
```
HEAD → feature/auth → [C6]
```

## 🔍 Verification Commands

```powershell
# Should show all your branches
git branch

# Should show graph
git log --oneline --graph --all

# Should show verbose info
git branch -v

# Should show current branch
git branch --show-current
```

## 💡 Pro Tips

### Branch Naming Conventions
```
feature/user-authentication
bugfix/login-error
hotfix/security-patch
release/v1.2.0
chore/update-dependencies
docs/api-documentation
```

### Useful Aliases
```powershell
git config --global alias.br branch
git config --global alias.co checkout
git config --global alias.sw switch
```

### Quick Branch Creation
```powershell
# Old way
git branch feature
git checkout feature

# Better way
git checkout -b feature

# New way
git switch -c feature
```

## ⚠️ Common Mistakes

1. **Deleting unmerged branches**: Use `-d` (safe) not `-D` (force)
2. **Switching with uncommitted changes**: Commit or stash first
3. **Working on wrong branch**: Check `git status` first!
4. **Creating branch from wrong starting point**: Always verify current branch

## 🎯 What You Should Know Now

- ✅ How to create branches
- ✅ How to switch between branches
- ✅ What HEAD and branch pointers are
- ✅ How to list and inspect branches
- ✅ How to rename and delete branches
- ✅ The difference between `checkout` and `switch`
- ✅ How branches isolate work

## ⏭️ Next Exercise
Ready for **Exercise 07: Merging Branches** to learn how to combine branch work!

---

**Time to Complete**: 25 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-05
