# Solution: Exercise 18 - Advanced Workflows

## Part 1: Feature Branch Workflow

```powershell
mkdir workflow-practice
cd workflow-practice
git init

# Setup main
git checkout -b main
"Initial" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "Initial commit"

# Feature branch
git checkout -b feature/user-auth
"// Authentication" | Out-File auth.js -Encoding UTF8
git add auth.js
git commit -m "feat: add authentication"
"// Login" | Add-Content auth.js
git commit -am "feat: implement login"

# Merge back
git checkout main
git merge feature/user-auth
git branch -d feature/user-auth
```

## Part 2: Git Flow Workflow

```powershell
# Initialize branches
git checkout -b main
"v1.0.0" | Out-File VERSION -Encoding UTF8
git add VERSION
git commit -m "Initial v1.0.0"
git tag v1.0.0

git checkout -b develop

# Feature development
git checkout -b feature/shopping-cart develop
"// Cart" | Out-File cart.js -Encoding UTF8
git add cart.js
git commit -m "feat: add cart"
git checkout develop
git merge --no-ff feature/shopping-cart
git branch -d feature/shopping-cart

# Release branch
git checkout -b release/1.1.0 develop
"v1.1.0" | Out-File VERSION -Encoding UTF8
git commit -am "Bump to 1.1.0"
"// Fix" | Add-Content cart.js
git commit -am "fix: release bug"

# Merge to main
git checkout main
git merge --no-ff release/1.1.0
git tag v1.1.0

# Merge back to develop
git checkout develop
git merge --no-ff release/1.1.0
git branch -d release/1.1.0

# Hotfix
git checkout -b hotfix/1.1.1 main
"// Security fix" | Add-Content cart.js
git commit -am "fix: security"
"v1.1.1" | Out-File VERSION -Encoding UTF8
git commit -am "Bump to 1.1.1"

git checkout main
git merge --no-ff hotfix/1.1.1
git tag v1.1.1

git checkout develop
git merge --no-ff hotfix/1.1.1
git branch -d hotfix/1.1.1
```

## Part 3: GitHub Flow

```powershell
# Simpler workflow
git checkout -b simple-main
"Production" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Production ready"

# Feature branch
git checkout -b feature/new-api
"API v1" | Out-File api.js -Encoding UTF8
git add api.js
git commit -m "feat: start API"
"API v2" | Out-File api.js -Encoding UTF8
git commit -am "feat: improve API"

# Merge (PR would happen on GitHub)
git checkout simple-main
git merge --no-ff feature/new-api
git branch -d feature/new-api

# Squash merge option
git checkout -b feature/cleanup
"Work" | Out-File cleanup.txt -Encoding UTF8
git add cleanup.txt
git commit -m "WIP"
"More" | Out-File cleanup.txt -Encoding UTF8
git commit -am "More work"

git checkout simple-main
git merge --squash feature/cleanup
git commit -m "feat: cleanup feature"
git branch -d feature/cleanup
```

## Part 4: Pull Request Best Practices

```powershell
# Good PR
git checkout -b feature/notification
"Notification system" | Out-File notify.js -Encoding UTF8
git add notify.js
git commit -m "feat: add user notification system

- Implements email notifications
- Adds push notifications
- Includes notification preferences
- Fixes #123

Breaking changes: None
Tested on: Windows, Mac, Linux"

# Address review feedback
"Updated" | Out-File notify.js -Encoding UTF8
git commit -am "review: address validation feedback"
"More changes" | Out-File notify.js -Encoding UTF8
git commit -am "review: add error handling"
```

## Part 5: Release Management

```powershell
# Semantic versioning
git checkout main

# PATCH (1.0.0 → 1.0.1)
"v1.0.1" | Out-File VERSION -Encoding UTF8
git commit -am "chore: bump to 1.0.1"
git tag v1.0.1

# MINOR (1.0.1 → 1.1.0)
"v1.1.0" | Out-File VERSION -Encoding UTF8
git commit -am "chore: bump to 1.1.0"
git tag v1.1.0

# MAJOR (1.1.0 → 2.0.0)
"v2.0.0" | Out-File VERSION -Encoding UTF8
git commit -am "chore: bump to 2.0.0 [BREAKING]"
git tag v2.0.0

# Pre-releases
git tag -a v3.0.0-alpha -m "Alpha"
git tag -a v3.0.0-beta -m "Beta"
git tag -a v3.0.0-rc1 -m "RC1"
git tag -a v3.0.0 -m "Stable"
```

## Workflow Comparison

| Workflow | Best For | Complexity |
|----------|----------|------------|
| Feature Branch | Small teams | ⭐ Simple |
| GitHub Flow | Continuous deployment | ⭐⭐ Moderate |
| Git Flow | Scheduled releases | ⭐⭐⭐ Complex |
| Forking | Open source | ⭐⭐ Moderate |

## Branch Naming

```
feature/description   - New features
bugfix/description    - Bug fixes
hotfix/description    - Emergency fixes
refactor/description  - Refactoring
docs/description      - Documentation
test/description      - Tests
```

## Merge Strategies

### Merge Commit (--no-ff)
- Preserves branch history
- Shows when merged
- Creates merge commit

### Squash Merge
- All commits → one
- Clean history
- Loses individual commits

### Rebase Merge
- Linear history
- No merge commits
- Rewrites history

## Verification

✅ You should now understand:
- Feature Branch workflow
- Git Flow workflow
- GitHub Flow workflow
- PR best practices
- Release management
- Branch naming conventions
- Merge strategies

## Pro Tips

### Keep Feature Updated
```powershell
git checkout feature
git rebase main  # Or: git merge main
```

### Protected Branches
- Protect main/develop
- Require PR reviews
- Require status checks
- No force push

### Commit Convention
```
feat: New feature
fix: Bug fix
docs: Documentation
refactor: Refactoring
test: Tests
chore: Maintenance
```

### Complete Workflow
```powershell
# 1. Update main
git checkout main
git pull

# 2. Create feature
git checkout -b feature/name

# 3. Work
git commit -am "feat: add feature"

# 4. Keep updated
git rebase main

# 5. Push & PR
# git push -u origin feature/name

# 6. After merge
git checkout main
git pull
git branch -d feature/name
```

---

**Exercise completed!** ✅  
You now understand professional Git workflows!
