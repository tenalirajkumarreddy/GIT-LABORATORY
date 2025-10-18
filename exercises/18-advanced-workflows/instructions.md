# Exercise 18: Advanced Git Workflows - Team Collaboration

## 🎯 Objective
Learn professional Git workflows used in real-world software development teams.

## 📚 Concepts Covered
- Git Flow workflow
- GitHub Flow workflow
- Feature branch workflow
- Forking workflow
- Pull Request (PR) process
- Code review practices
- Release management
- Hotfix procedures

## 🌟 Overview
Different teams use different workflows. Learn when and how to use each.

## 📝 Preparation

```powershell
mkdir workflow-practice
cd workflow-practice
git init
```

## Part 1: Feature Branch Workflow

### Task 1: Basic Feature Branch Flow
The simplest workflow:

```powershell
# Setup main branch
git checkout -b main
"Initial project" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "Initial commit"

# Create feature branch
git checkout -b feature/user-auth
"// User authentication" | Out-File auth.js -Encoding UTF8
git add auth.js
git commit -m "feat: add user authentication"

# More work on feature
"// Login logic" | Add-Content auth.js
git commit -am "feat: implement login"

# Merge back to main
git checkout main
git merge feature/user-auth

# Delete feature branch (cleanup)
git branch -d feature/user-auth
```

### Task 2: Feature Branch Best Practices
Professional naming and workflow:

```powershell
# Good branch names:
git checkout -b feature/add-payment
git checkout -b bugfix/fix-login-error
git checkout -b hotfix/critical-security-fix
git checkout -b refactor/improve-performance

# Bad branch names (avoid):
# git checkout -b test
# git checkout -b new-feature
# git checkout -b johns-branch
```

### Task 3: Feature Branch with Conflicts
Handle conflicts professionally:

```powershell
# Setup
git checkout main
"version 1" | Out-File config.js -Encoding UTF8
git add config.js
git commit -m "Add config"

# Feature branch
git checkout -b feature/config-update
"version 2 from feature" | Out-File config.js -Encoding UTF8
git commit -am "feat: update config"

# Meanwhile, main updated
git checkout main
"version 2 from main" | Out-File config.js -Encoding UTF8
git commit -am "Update config"

# Try to merge - conflict!
git merge feature/config-update

# Resolve:
# 1. Open config.js
# 2. Choose version or combine
# 3. Remove conflict markers
# 4. git add config.js
# 5. git commit
```

## Part 2: Git Flow Workflow

### Task 4: Git Flow Branches
Git Flow uses specific branches:

```
main       - Production-ready code
develop    - Integration branch
feature/*  - New features
release/*  - Release preparation
hotfix/*   - Emergency fixes
```

Setup:
```powershell
# Initialize Git Flow structure
git checkout -b main
"v1.0.0" | Out-File VERSION -Encoding UTF8
git add VERSION
git commit -m "Initial release v1.0.0"
git tag v1.0.0

# Create develop branch
git checkout -b develop
```

### Task 5: Feature Development (Git Flow)
```powershell
# Start feature from develop
git checkout develop
git checkout -b feature/shopping-cart

# Work on feature
"// Shopping cart" | Out-File cart.js -Encoding UTF8
git add cart.js
git commit -m "feat: add shopping cart"

"// Add to cart function" | Add-Content cart.js
git commit -am "feat: implement add to cart"

# Finish feature - merge to develop
git checkout develop
git merge --no-ff feature/shopping-cart -m "Merge feature/shopping-cart into develop"

# Delete feature branch
git branch -d feature/shopping-cart
```

### Task 6: Release Branch (Git Flow)
```powershell
# Create release branch from develop
git checkout develop
git checkout -b release/1.1.0

# Prepare release (version bumps, changelog, etc.)
"v1.1.0" | Out-File VERSION -Encoding UTF8
git commit -am "Bump version to 1.1.0"

# Bug fixes only on release branch
"// Fix for release" | Add-Content cart.js
git commit -am "fix: resolve cart calculation error"

# Merge to main (production)
git checkout main
git merge --no-ff release/1.1.0 -m "Release v1.1.0"
git tag v1.1.0

# Merge back to develop
git checkout develop
git merge --no-ff release/1.1.0 -m "Merge release/1.1.0 back to develop"

# Delete release branch
git branch -d release/1.1.0
```

### Task 7: Hotfix Branch (Git Flow)
```powershell
# Emergency fix needed in production!
git checkout main
git checkout -b hotfix/1.1.1

# Make fix
"// Critical security fix" | Add-Content auth.js
git commit -am "fix: patch critical security vulnerability"

# Update version
"v1.1.1" | Out-File VERSION -Encoding UTF8
git commit -am "Bump version to 1.1.1"

# Merge to main
git checkout main
git merge --no-ff hotfix/1.1.1 -m "Hotfix v1.1.1"
git tag v1.1.1

# Merge to develop too
git checkout develop
git merge --no-ff hotfix/1.1.1 -m "Merge hotfix/1.1.1"

# Delete hotfix branch
git branch -d hotfix/1.1.1
```

## Part 3: GitHub Flow Workflow

### Task 8: GitHub Flow (Simpler)
GitHub Flow is lighter than Git Flow:

```
main        - Always deployable
feature/*   - All work happens here
```

```powershell
# Reset for clean demo
git checkout -b simple-main
"Production ready" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Production ready code"

# Create feature
git checkout -b feature/new-api

# Multiple commits (it's okay!)
"API v1" | Out-File api.js -Encoding UTF8
git add api.js
git commit -m "feat: start API"

"API v2" | Out-File api.js -Encoding UTF8
git commit -am "feat: improve API"

"API v3" | Out-File api.js -Encoding UTF8
git commit -am "docs: add API docs"

# Create PR (simulated)
# On GitHub: compare feature/new-api with main

# After review, merge
git checkout simple-main
git merge --no-ff feature/new-api -m "Merge PR #42: New API"

# Deploy immediately after merge
# Delete branch
git branch -d feature/new-api
```

### Task 9: GitHub Flow with Squash Merge
```powershell
git checkout simple-main
git checkout -b feature/cleanup

# Multiple small commits
"commit 1" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "WIP"

"commit 2" | Out-File file.txt -Encoding UTF8
git commit -am "More work"

"commit 3" | Out-File file.txt -Encoding UTF8
git commit -am "Final version"

# Squash merge (all commits → 1)
git checkout simple-main
git merge --squash feature/cleanup
git commit -m "feat: implement cleanup feature

- Cleaned up code
- Improved performance
- Added tests"

# Feature branch commits are squashed into one
git branch -d feature/cleanup
```

## Part 4: Forking Workflow

### Task 10: Forking Workflow (Open Source)
Simulate the forking workflow:

```powershell
# Setup "upstream" repo (original)
mkdir upstream-repo
cd upstream-repo
git init
"Original project" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "Initial commit"

cd ..

# Fork (simulate with clone)
git clone upstream-repo my-fork
cd my-fork

# Add upstream remote
git remote add upstream ../upstream-repo

# Create feature branch
git checkout -b feature/contribution

# Make changes
"My contribution" | Out-File contribution.md -Encoding UTF8
git add contribution.md
git commit -m "feat: add my contribution"

# Push to your fork
# git push origin feature/contribution

# Create PR from your fork to upstream
# (On GitHub: my-fork/feature/contribution -> upstream/main)

# After PR accepted, sync your fork
git checkout main
git fetch upstream
git merge upstream/main
```

## Part 5: Pull Request Best Practices

### Task 11: Creating Good PRs
What makes a good Pull Request:

```powershell
# 1. Small, focused changes
git checkout -b feature/small-improvement
"Small change" | Out-File small.js -Encoding UTF8
git add small.js
git commit -m "feat: add small improvement

- Implements feature X
- Fixes #123
- Adds tests

Breaking changes: None"

# 2. Good PR title/description
# Title: "feat: Add user notification system"
# Description:
# - What changed
# - Why
# - How to test
# - Screenshots if UI change
# - Related issues
```

### Task 12: PR Review Process
Simulate code review workflow:

```powershell
# Reviewer requests changes
git checkout feature/small-improvement

# Make requested changes
"Updated based on review" | Out-File small.js -Encoding UTF8
git commit -am "review: address feedback on validation"

# Add more commits based on review
"More changes" | Out-File small.js -Encoding UTF8
git commit -am "review: add error handling"

# Final approval → merge
git checkout main
git merge --no-ff feature/small-improvement -m "Merge PR #45: Add small improvement"
```

### Task 13: Draft Pull Requests
Use draft PRs for early feedback:

```powershell
git checkout -b feature/wip-feature

# Partial work
"Work in progress" | Out-File wip.js -Encoding UTF8
git add wip.js
git commit -m "wip: draft PR for early feedback"

# On GitHub: Create Draft PR
# Get feedback before finishing
# Mark as "Ready for review" when done
```

## Part 6: Release Management

### Task 14: Semantic Versioning Releases
```powershell
# Version format: MAJOR.MINOR.PATCH
git checkout main

# PATCH: Bug fixes (1.0.0 → 1.0.1)
"v1.0.1" | Out-File VERSION -Encoding UTF8
git commit -am "chore: bump version to 1.0.1"
git tag v1.0.1

# MINOR: New features (1.0.1 → 1.1.0)
"v1.1.0" | Out-File VERSION -Encoding UTF8
git commit -am "chore: bump version to 1.1.0"
git tag v1.1.0

# MAJOR: Breaking changes (1.1.0 → 2.0.0)
"v2.0.0" | Out-File VERSION -Encoding UTF8
git commit -am "chore: bump version to 2.0.0 [BREAKING]"
git tag v2.0.0
```

### Task 15: Complete Team Workflow
Put it all together:

```powershell
# 1. Start from updated main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/user-dashboard

# 3. Work on feature
"Dashboard UI" | Out-File dashboard.js -Encoding UTF8
git add dashboard.js
git commit -m "feat: add user dashboard UI"

# 4. Keep feature updated with main
git fetch origin
git rebase origin/main

# 5. Push feature branch
# git push -u origin feature/user-dashboard

# 6. Create PR on GitHub
# - Add description
# - Link issues
# - Request reviewers

# 7. Address review feedback
"Review changes" | Out-File dashboard.js -Encoding UTF8
git commit -am "review: improve dashboard performance"
# git push

# 8. Approved! Merge PR (on GitHub)
# Choose merge strategy:
# - Merge commit (keeps all commits)
# - Squash (one commit)
# - Rebase (linear history)

# 9. Delete feature branch
git checkout main
git pull origin main
git branch -d feature/user-dashboard

# 10. Repeat for next feature
```

## ✅ Expected Outcome

You should understand:
- When to use each workflow
- How to work with feature branches
- PR creation and review process
- Release management strategies
- Team collaboration best practices

## 🎓 Key Concepts

### Workflow Comparison

| Workflow | Best For | Complexity |
|----------|----------|------------|
| Feature Branch | Small teams | ⭐ Simple |
| GitHub Flow | Continuous deployment | ⭐⭐ Moderate |
| Git Flow | Scheduled releases | ⭐⭐⭐ Complex |
| Forking | Open source | ⭐⭐ Moderate |

### Branch Naming Conventions
```
feature/description   - New features
bugfix/description    - Bug fixes
hotfix/description    - Emergency fixes
refactor/description  - Code refactoring
docs/description      - Documentation
test/description      - Test additions
```

### Merge Strategies

**Merge Commit (--no-ff):**
- Preserves branch history
- Shows when feature was merged
- Creates merge commit

**Squash Merge:**
- All commits → one commit
- Clean linear history
- Loses individual commits

**Rebase Merge:**
- Linear history
- No merge commits
- Rewrites history

## 🔍 Verification Commands

```powershell
# View branch structure
git log --graph --oneline --all

# See merged branches
git branch --merged

# See unmerged branches
git branch --no-merged

# View branch history
gitk --all
```

## 💡 Pro Tips

### Keeping Feature Branch Updated
```powershell
# Option 1: Merge main into feature
git checkout feature/x
git merge main

# Option 2: Rebase feature on main (cleaner)
git checkout feature/x
git rebase main
```

### Protected Branches
In team settings:
- Protect main/develop branches
- Require PR reviews
- Require status checks
- Require up-to-date branch
- No force push

### Commit Message Convention
```
feat: Add new feature
fix: Fix bug
docs: Update documentation
refactor: Refactor code
test: Add tests
chore: Update build tasks
```

## 🎯 What You Should Know Now

- ✅ Different Git workflows (Feature Branch, Git Flow, GitHub Flow, Forking)
- ✅ When to use each workflow
- ✅ How to create and review PRs
- ✅ Release management strategies
- ✅ Branch naming conventions
- ✅ Merge strategies
- ✅ Team collaboration best practices

## 📊 Workflow Cheatsheet

```powershell
# Feature Branch Workflow
git checkout -b feature/name
# work...
git checkout main
git merge feature/name

# Git Flow
git checkout -b feature/name develop  # New feature
git checkout -b release/1.0.0 develop # Release prep
git checkout -b hotfix/1.0.1 main     # Emergency fix

# GitHub Flow
git checkout -b feature/name
# work, push, create PR
git checkout main
git merge feature/name

# Forking Workflow
git remote add upstream <url>
git fetch upstream
git merge upstream/main
```

## 🎬 Real-World Scenarios

### Scenario 1: Small Startup Team
Use GitHub Flow:
- Simple and fast
- Continuous deployment
- Everyone works on main

### Scenario 2: Large Enterprise
Use Git Flow:
- Multiple release versions
- QA/staging environments
- Scheduled releases

### Scenario 3: Open Source Project
Use Forking Workflow:
- External contributors
- Maintainer control
- PR review process

## ⏭️ Next Exercise
Move on to **Exercise 19: Scenario - Wrong Branch** for mistake recovery!

---

**Time to Complete**: 45 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: All previous exercises  
**💼 Real-world application**: This is how professional teams work!
