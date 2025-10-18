# Solution: Exercise 06 - Branching Basics

## Complete Command Sequence

```powershell
# Preparation
mkdir branching-practice
cd branching-practice
git init
"Initial content" | Out-File main.txt -Encoding UTF8
git add main.txt
git commit -m "Initial commit"

# Task 1: Create new branch
git branch feature
git branch  # List branches, * shows current

# Task 2: Switch to branch
git checkout feature
git branch  # Now * is on feature

# Task 3: Create and switch in one command
git checkout -b another-feature
git branch  # Shows all three branches

# Task 4: Make changes on branch
"Feature work" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "Add feature"
git log --oneline  # Shows commit on feature branch

# Task 5: Switch back to main
git checkout main
# feature.txt doesn't exist on main!
Get-Content feature.txt  # Error: file not found

# Task 6: View all branches
git branch
git branch -a  # All branches including remote
git branch -v  # With last commit

# Task 7: Create branch from specific commit
git log --oneline
git branch old-work <commit-hash>

# Task 8: Rename branch
git branch -m another-feature renamed-feature
git branch  # Shows renamed branch

# Task 9: Delete branch
git branch -d old-work
git branch  # Branch removed

# Task 10: Force delete unmerged branch
git checkout -b temp-branch
"Unmerged work" | Out-File temp.txt -Encoding UTF8
git add temp.txt
git commit -m "Temp work"
git checkout main
git branch -d temp-branch  # Error: not merged
git branch -D temp-branch  # Force delete

# Task 11: View branch differences
git checkout -b dev
"Dev changes" | Out-File dev.txt -Encoding UTF8
git add dev.txt
git commit -m "Dev changes"
git diff main..dev
git log main..dev --oneline

# Task 12: Branch from another branch
git checkout feature
git checkout -b feature-sub
"Sub feature" | Out-File sub.txt -Encoding UTF8
git add sub.txt
git commit -m "Sub feature"

# Task 13: Track remote branch
# Simulated (would need real remote)
# git checkout -b local-feature origin/remote-feature
# git branch -u origin/feature

# Task 14: List merged branches
git checkout main
git merge feature  # Merge feature into main
git branch --merged  # Shows merged branches
git branch --no-merged  # Shows unmerged

# Task 15: Branch workflow
git checkout main
git checkout -b feature/login
"Login code" | Out-File login.js -Encoding UTF8
git add login.js
git commit -m "feat: add login"
git checkout main
git merge feature/login
git branch -d feature/login
```

## Key Concepts

### Branches Are Pointers
- Branch = pointer to a commit
- Lightweight (just 41 bytes!)
- HEAD = pointer to current branch
- Creating branch doesn't change files

### Branch Types
```
main/master  - Default branch
feature/*    - New features
bugfix/*     - Bug fixes
hotfix/*     - Emergency fixes
release/*    - Release preparation
```

## Important Commands

```powershell
# Create branch
git branch <name>

# Switch branch
git checkout <name>
git switch <name>  # New command (Git 2.23+)

# Create and switch
git checkout -b <name>
git switch -c <name>  # New command

# List branches
git branch           # Local only
git branch -a        # All (local + remote)
git branch -v        # With last commit
git branch --merged  # Merged into current

# Rename branch
git branch -m <old> <new>

# Delete branch
git branch -d <name>   # Safe delete (only if merged)
git branch -D <name>   # Force delete

# View differences
git diff branch1..branch2
git log branch1..branch2
```

## Common Workflows

### Feature Branch Flow
```powershell
# Start feature
git checkout -b feature/new-thing
# ... work ...
git add .
git commit -m "Implement feature"

# Finish feature
git checkout main
git merge feature/new-thing
git branch -d feature/new-thing
```

### Switching Between Features
```powershell
# Working on feature A
git checkout -b feature-a
# ... work ...

# Need to switch to feature B
git add .
git commit -m "WIP: feature A"
git checkout -b feature-b
# ... work on B ...

# Back to feature A
git checkout feature-a
```

## Verification

✅ You should now understand:
- What branches are (pointers to commits)
- How to create and delete branches
- Switching between branches
- Branch naming conventions
- Viewing branch information
- When to use branches

## Pro Tips

### Branch Naming
```powershell
# Good names
feature/user-authentication
bugfix/login-error
hotfix/critical-security
release/v1.2.0

# Bad names
test
new-branch
johns-work
branch1
```

### Quick Branch Info
```powershell
# Current branch
git branch --show-current

# Last commit on each branch
git branch -v

# Branches with specific commit
git branch --contains <commit>
```

### Clean Up Merged Branches
```powershell
# List merged branches
git branch --merged | grep -v "main"

# Delete them
git branch --merged | grep -v "main" | ForEach-Object { git branch -d $_.Trim() }
```

---

**Exercise completed!** ✅  
You now understand Git branches!
