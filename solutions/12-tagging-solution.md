# Solution: Exercise 12 - Tagging

## Complete Command Sequence

```powershell
# Preparation
mkdir tagging-practice
cd tagging-practice
git init
"v1.0.0 code" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "Initial release"

# Task 1: Create lightweight tag
git tag v1.0.0
git tag  # Lists tags
git show v1.0.0  # Shows commit

# Task 2: Create annotated tag
git tag -a v1.1.0 -m "Release version 1.1.0 - New features"
git show v1.1.0
# Shows tagger, date, message, and commit

# Task 3: Tag previous commit
git log --oneline
git tag -a v0.9.0 <commit-hash> -m "Beta release"
git tag  # Shows v0.9.0

# Task 4: List tags with pattern
git tag v1.0.0-beta
git tag v1.0.0-rc1
git tag v2.0.0-alpha
git tag  # All tags
git tag -l "v1.*"  # Only v1 tags
git tag -l "*beta*"  # Tags with "beta"

# Task 5: View tag details
git show v1.1.0
git tag -n  # Tags with messages
git log v1.1.0  # Log up to tag

# Task 6: Push tags to remote
# git push origin v1.1.0     # Push single tag
# git push origin --tags     # Push all tags
# git push --follow-tags     # Push annotated tags only

# Task 7: Delete local tag
git tag temp
git tag  # temp exists
git tag -d temp
git tag  # temp gone

# Task 8: Delete remote tag
# git push origin :refs/tags/v1.0.0-beta
# Or newer syntax:
# git push origin --delete v1.0.0-beta

# Task 9: Checkout a tag
git checkout v1.0.0
# Detached HEAD state
git log --oneline -3
git checkout main  # Return

# Task 10: Create branch from tag
git checkout v1.0.0
git checkout -b hotfix-v1.0
"hotfix" | Out-File fix.js -Encoding UTF8
git add fix.js
git commit -m "Hotfix for v1.0.0"
git tag -a v1.0.1 -m "Hotfix release"

# Task 11: Semantic versioning
git checkout main
git tag -a v2.0.0 -m "MAJOR: Breaking changes"
"New feature" | Out-File feature.js -Encoding UTF8
git add feature.js
git commit -m "Add feature"
git tag -a v2.1.0 -m "MINOR: New features"
"Bug fix" | Out-File feature.js -Encoding UTF8
git commit -am "Fix bug"
git tag -a v2.1.1 -m "PATCH: Bug fixes"

# Task 12: Pre-release tags
git tag -a v3.0.0-alpha -m "Alpha release"
git tag -a v3.0.0-beta -m "Beta release"
git tag -a v3.0.0-rc1 -m "Release candidate 1"
git tag -a v3.0.0 -m "Stable release"

# Task 13: View commits between tags
git log v1.0.0..v2.0.0 --oneline
# Shows all commits between versions

# Task 14: Tag naming conventions
git tag -a v1.0.0 -m "With v prefix"
git tag -a 2.0.0 -m "Without v"
git tag -a release-2025-10-18 -m "Date-based"
git tag -a Mercury-Release -m "Name-based"

# Task 15: Complete release process
git checkout main
git status  # Clean
"const version = '2.0.0';" | Out-File version.js -Encoding UTF8
git add version.js
git commit -m "Bump version to 2.0.0"
git tag -a v2.0.0 -m "Release v2.0.0

Features:
- New user dashboard
- Performance improvements
- Bug fixes

Breaking changes:
- API endpoint structure changed"
git log --oneline --decorate -5
```

## Tag Types

### Lightweight Tag
```powershell
git tag v1.0.0
```
- Just a pointer to commit
- No additional information
- 41 bytes (SHA-1 hash)
- Use for: Private/temporary marks

### Annotated Tag (Recommended)
```powershell
git tag -a v1.0.0 -m "Release 1.0.0"
```
- Full Git object
- Stores: tagger name, email, date
- Has message
- Can be GPG signed
- Use for: Releases

## Semantic Versioning

Format: `MAJOR.MINOR.PATCH`

```
v1.0.0 → v1.0.1  # PATCH: Bug fixes
v1.0.1 → v1.1.0  # MINOR: New features (backward compatible)
v1.1.0 → v2.0.0  # MAJOR: Breaking changes

Pre-releases:
v2.0.0-alpha
v2.0.0-beta
v2.0.0-rc1
v2.0.0
```

## Important Commands

```powershell
# Create tags
git tag <name>                    # Lightweight
git tag -a <name> -m "message"    # Annotated
git tag -a <name> <commit> -m "msg"  # Tag old commit

# List tags
git tag                           # All tags
git tag -l "v1.*"                 # Pattern match
git tag -n                        # With messages

# View tags
git show <tag>                    # Tag details
git log <tag>                     # Log up to tag

# Delete tags
git tag -d <tag>                  # Local delete
git push origin --delete <tag>    # Remote delete

# Push tags
git push origin <tag>             # Push one tag
git push origin --tags            # Push all tags
git push --follow-tags            # Push annotated only

# Checkout tags
git checkout <tag>                # Detached HEAD
git checkout -b <branch> <tag>    # Create branch

# Compare tags
git diff v1.0.0 v2.0.0
git log v1.0.0..v2.0.0
```

## When to Tag

### ✅ Good Use Cases
- Release versions (v1.0.0, v2.1.3)
- Milestones
- Deployment points
- Stable points in history
- Points you want to reference later

### ❌ Don't Tag
- Every commit (too many!)
- Work in progress
- Experimental code
- Personal bookmarks (use branches)

## Common Workflows

### Release Workflow
```powershell
# Finish features
git checkout main
git pull

# Update version
echo "v1.2.0" > VERSION
git commit -am "Bump version to 1.2.0"

# Create tag
git tag -a v1.2.0 -m "Release 1.2.0

- Feature X
- Feature Y
- Bug fix Z"

# Push
git push origin main
git push origin v1.2.0

# Create GitHub Release (on GitHub)
```

### Hotfix Workflow
```powershell
# Checkout release tag
git checkout v1.0.0

# Create hotfix branch
git checkout -b hotfix/v1.0.1

# Fix bug
git commit -am "fix: critical bug"

# Tag hotfix
git tag -a v1.0.1 -m "Hotfix: critical bug fix"

# Merge back
git checkout main
git merge hotfix/v1.0.1
git push --tags
```

## Verification

✅ You should now understand:
- Lightweight vs annotated tags
- Creating and deleting tags
- Semantic versioning
- Tag naming conventions
- Pushing tags to remote
- Creating branches from tags
- When to use tags

## Pro Tips

### Find Latest Tag
```powershell
git describe --tags --abbrev=0
```

### Find Tag Containing Commit
```powershell
git tag --contains <commit>
```

### Create Signed Tags
```powershell
git tag -s v1.0.0 -m "Signed release"
# Requires GPG key
```

### View All Tagged Commits
```powershell
git log --oneline --decorate
```

### Compare with Latest Tag
```powershell
git diff $(git describe --tags --abbrev=0)
```

---

**Exercise completed!** ✅  
You now understand Git tags and versioning!
