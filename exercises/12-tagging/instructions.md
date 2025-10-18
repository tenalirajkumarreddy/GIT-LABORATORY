# Exercise 12: Tagging - Marking Milestones

## 🎯 Objective
Learn to use Git tags to mark important points in history like releases and versions.

## 📚 Concepts Covered
- Lightweight tags
- Annotated tags  
- Creating tags
- Listing tags
- Pushing tags
- Deleting tags
- Checking out tags
- Semantic versioning

## 📝 Preparation

```powershell
mkdir tagging-practice
cd tagging-practice
git init

# Create some history
"v1.0.0 code" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Initial release"

"v1.1.0 code" | Out-File app.js -Encoding UTF8
git commit -am "Add minor features"

"v1.1.1 code" | Out-File app.js -Encoding UTF8
git commit -am "Bug fixes"
```

## 📝 Tasks

### Task 1: Create Lightweight Tag
1. Tag current commit: `git tag v1.1.1`
2. List tags: `git tag`
3. View tag info: `git show v1.1.1`
4. Lightweight tag = just a pointer (like branch but doesn't move)

### Task 2: Create Annotated Tag (Recommended)
1. Tag with message: `git tag -a v1.2.0 -m "Release version 1.2.0 - New features"`
2. View tag: `git show v1.2.0`
3. Shows: tagger name, date, message, commit
4. Annotated = full Git object (recommended for releases)

### Task 3: Tag Previous Commit
1. View log: `git log --oneline`
2. Tag older commit: `git tag -a v1.0.0 <commit-hash> -m "First release"`
3. List tags: See v1.0.0

### Task 4: List Tags with Pattern
1. Create multiple tags:
   ```powershell
   git tag v1.0.0-beta
   git tag v1.0.0-rc1
   git tag v2.0.0-alpha
   ```
2. List all: `git tag`
3. List v1 only: `git tag -l "v1.*"`
4. List betas: `git tag -l "*beta*"`

### Task 5: View Tag Details
1. Show tag info: `git show v1.2.0`
2. Show just message: `git tag -n`
3. Show with commit: `git log v1.2.0`

### Task 6: Push Tags to Remote
(Simulated - would work with real remote)
```powershell
# Push single tag
git push origin v1.2.0

# Push all tags
git push origin --tags

# Or newer method
git push --follow-tags  # Pushes annotated tags only
```

### Task 7: Delete Local Tag
1. Create temp tag: `git tag temp`
2. List: `git tag`
3. Delete: `git tag -d temp`
4. List: Gone!

### Task 8: Delete Remote Tag
(Command for real remotes)
```powershell
# Delete from remote
git push origin :refs/tags/v1.0.0-beta

# Or newer syntax
git push origin --delete v1.0.0-beta
```

### Task 9: Checkout a Tag
1. Checkout tag: `git checkout v1.0.0`
2. You're in "detached HEAD" state
3. View files at that version
4. Return to main: `git checkout main`

### Task 10: Create Branch from Tag
1. Checkout tag: `git checkout v1.0.0`
2. Create branch: `git checkout -b hotfix-v1.0`
3. Make fixes: 
   ```powershell
   "hotfix" | Out-File fix.js -Encoding UTF8
   git add fix.js
   git commit -m "Hotfix for v1.0.0"
   ```
4. Tag the fix: `git tag -a v1.0.1 -m "Hotfix release"`

### Task 11: Semantic Versioning
Practice semantic versioning (MAJOR.MINOR.PATCH):

```powershell
# MAJOR version: Breaking changes
git tag -a v2.0.0 -m "Breaking: New API structure"

# MINOR version: New features (backward compatible)
git tag -a v2.1.0 -m "Add new payment method"

# PATCH version: Bug fixes
git tag -a v2.1.1 -m "Fix payment rounding error"
```

### Task 12: Pre-release Tags
1. Alpha: `git tag -a v3.0.0-alpha -m "Alpha release for testing"`
2. Beta: `git tag -a v3.0.0-beta -m "Beta release"`
3. Release Candidate: `git tag -a v3.0.0-rc1 -m "Release candidate 1"`
4. Final: `git tag -a v3.0.0 -m "Stable release"`

### Task 13: View Commits Between Tags
1. See commits between versions:
   ```powershell
   git log v1.0.0..v2.0.0 --oneline
   ```
2. Generate changelog
3. See what changed between releases

### Task 14: Tag Naming Conventions
Practice different conventions:

```powershell
# With 'v' prefix (common)
git tag -a v1.0.0 -m "Version 1.0.0"

# Without 'v'
git tag -a 1.0.0 -m "Version 1.0.0"

# Date-based
git tag -a release-2025-10-18 -m "October 2025 release"

# Name-based
git tag -a "Mercury-Release" -m "Mercury feature set"
```

### Task 15: Tag Best Practices
Create a release with full process:

```powershell
# 1. Make sure working directory is clean
git status

# 2. Update version in files if needed
"const version = '2.0.0';" | Out-File version.js -Encoding UTF8
git add version.js
git commit -m "Bump version to 2.0.0"

# 3. Create annotated tag
git tag -a v2.0.0 -m "Release v2.0.0

Features:
- New user dashboard
- Performance improvements
- Bug fixes

Breaking changes:
- API endpoint structure changed"

# 4. Push tag
# git push origin v2.0.0

# 5. Create GitHub release (on GitHub)
```

## ✅ Expected Outcome

You should know:
- Difference between lightweight and annotated tags
- How to create, list, and delete tags
- Semantic versioning
- When to use tags
- How to work with tags and remotes

## 🎓 Key Concepts

### Lightweight vs Annotated Tags

**Lightweight Tag:**
- Just a pointer to commit
- No extra information
- Use for private/temporary marks

**Annotated Tag:**
- Full Git object
- Stores tagger name, email, date
- Has message
- Can be signed with GPG
- **Recommended for releases**

### Semantic Versioning (SemVer)
```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes (1.0.0 → 2.0.0)
MINOR: New features, backward compatible (1.0.0 → 1.1.0)
PATCH: Bug fixes (1.0.0 → 1.0.1)

Pre-release: 1.0.0-alpha, 1.0.0-beta, 1.0.0-rc1
```

### When to Tag
- ✅ Release versions
- ✅ Milestones
- ✅ Deployment points
- ✅ Stable points
- ❌ Every commit (too many)
- ❌ Work in progress

## 🔍 Verification Commands

```powershell
# List all tags
git tag

# List with pattern
git tag -l "v1.*"

# Show tag details
git show v1.0.0

# Show tags with messages
git tag -n

# See what's tagged
git log --oneline --decorate
```

## 💡 Pro Tips

### Tagging Workflow
```powershell
# After finishing feature for release
git checkout main
git pull
git tag -a v1.2.0 -m "Release 1.2.0"
git push origin v1.2.0
```

### Finding Tags
```powershell
# Find tag pointing to commit
git describe --tags

# Find tags containing commit
git tag --contains <commit>

# Latest tag
git describe --tags --abbrev=0
```

### Tag Messages
```powershell
# Good tag message
git tag -a v2.0.0 -m "Major release v2.0.0

New features:
- Feature A
- Feature B

Breaking changes:
- Old API removed

Bug fixes:
- Fix #123
- Fix #456"
```

## 🎯 What You Should Know Now

- ✅ How to create lightweight and annotated tags
- ✅ How to list and filter tags
- ✅ How to push tags to remote
- ✅ How to delete tags
- ✅ Semantic versioning
- ✅ When to use tags
- ✅ Tag naming conventions

## 📊 Tagging Commands Cheatsheet

```powershell
# Create lightweight tag
git tag v1.0.0

# Create annotated tag
git tag -a v1.0.0 -m "Release 1.0.0"

# Tag specific commit
git tag -a v1.0.0 <hash> -m "message"

# List tags
git tag
git tag -l "v1.*"

# Show tag
git show v1.0.0

# Delete local tag
git tag -d v1.0.0

# Push tag
git push origin v1.0.0
git push origin --tags

# Delete remote tag
git push origin --delete v1.0.0

# Checkout tag
git checkout v1.0.0

# Create branch from tag
git checkout -b branch-name v1.0.0
```

## 📦 GitHub Release Integration

Tags are typically used with GitHub Releases:

1. Create tag locally
2. Push tag to GitHub
3. Go to GitHub → Releases
4. Create release from tag
5. Add release notes
6. Attach binaries if needed

## ⏭️ Next Exercise
Move on to **Exercise 13: Cherry-Picking** for selective commit application!

---

**Time to Complete**: 25 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-11
