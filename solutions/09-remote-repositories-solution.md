# Solution: Exercise 09 - Remote Repositories

## Complete Command Sequence

```powershell
# Preparation - Create "remote" repository
mkdir remote-practice
cd remote-practice
mkdir origin-repo
cd origin-repo
git init --bare
cd ..

# Task 1: Clone repository
git clone origin-repo local-repo
cd local-repo
git remote -v  # Shows origin

# Task 2: View remote information
git remote
git remote -v  # With URLs
git remote show origin  # Detailed info

# Task 3: Add remote
cd ..
mkdir another-local
cd another-local
git init
"Content" | Out-File file.txt -Encoding UTF8
git add file.txt
git commit -m "Initial commit"
git remote add origin ../origin-repo
git remote -v

# Task 4: Fetch from remote
cd ../local-repo
"Change" | Out-File newfile.txt -Encoding UTF8
git add newfile.txt
git commit -m "Add newfile"
git push origin main
cd ../another-local
git fetch origin
git log origin/main  # See fetched commits

# Task 5: Pull from remote
git pull origin main
# Fetches and merges in one command
Get-Content newfile.txt  # File now exists

# Task 6: Push to remote
"Local change" | Out-File local.txt -Encoding UTF8
git add local.txt
git commit -m "Local change"
git push origin main

# Task 7: Create remote branch
git checkout -b feature
"Feature" | Out-File feature.txt -Encoding UTF8
git add feature.txt
git commit -m "Feature work"
git push -u origin feature
# -u sets upstream tracking

# Task 8: Track remote branch
git checkout -b local-feature origin/feature
# Creates local branch tracking remote

# Task 9: View tracking branches
git branch -vv  # Shows tracking info

# Task 10: Fetch vs Pull
git fetch origin  # Downloads but doesn't merge
git log origin/main  # View remote changes
git merge origin/main  # Manual merge
# vs
git pull origin main  # Fetch + merge in one

# Task 11: Push with upstream
git checkout -b new-branch
"New" | Out-File new.txt -Encoding UTF8
git add new.txt
git commit -m "New branch"
git push -u origin new-branch
# Sets upstream for future pushes

# Task 12: Delete remote branch
git push origin --delete feature
# Or:
git push origin :feature

# Task 13: Rename remote
git remote rename origin upstream
git remote -v  # Shows new name

# Task 14: Change remote URL
git remote set-url upstream ../new-origin-repo
git remote -v  # Shows new URL

# Task 15: Multiple remotes
git remote add fork ../fork-repo
git remote add upstream-official ../upstream-repo
git remote -v  # Shows all remotes
git fetch --all  # Fetch from all remotes
```

## Real-World Scenarios

### Clone from GitHub
```powershell
git clone https://github.com/user/repo.git
cd repo
git remote -v
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)
```

### Fork Workflow
```powershell
# Clone your fork
git clone https://github.com/you/repo.git
cd repo

# Add upstream (original repo)
git remote add upstream https://github.com/original/repo.git

# Fetch from upstream
git fetch upstream

# Sync with upstream
git checkout main
git merge upstream/main
git push origin main
```

### Multiple Remotes
```powershell
git remote add github https://github.com/user/repo.git
git remote add gitlab https://gitlab.com/user/repo.git
git remote add bitbucket https://bitbucket.org/user/repo.git

# Push to all
git push github main
git push gitlab main
git push bitbucket main
```

## Important Commands

```powershell
# Clone
git clone <url>
git clone <url> <directory>

# View remotes
git remote                    # List remotes
git remote -v                 # With URLs
git remote show <name>        # Detailed info

# Add/Remove remotes
git remote add <name> <url>
git remote remove <name>
git remote rename <old> <new>
git remote set-url <name> <url>

# Fetch
git fetch <remote>            # Fetch all branches
git fetch <remote> <branch>   # Fetch specific branch
git fetch --all               # Fetch from all remotes

# Pull
git pull <remote> <branch>
git pull --rebase             # Pull with rebase

# Push
git push <remote> <branch>
git push -u <remote> <branch> # Set upstream
git push --all                # Push all branches
git push --tags               # Push tags

# Branches
git branch -r                 # Remote branches
git branch -a                 # All branches
git branch -vv                # With tracking info

# Delete remote branch
git push <remote> --delete <branch>
git push <remote> :<branch>
```

## Fetch vs Pull

### Fetch (Safe)
```powershell
git fetch origin
# Downloads changes
# Doesn't modify working directory
# Review with: git log origin/main
# Then: git merge origin/main
```

### Pull (Convenient)
```powershell
git pull origin main
# = git fetch origin + git merge origin/main
# Automatically merges
# Can cause conflicts
```

## Upstream Tracking

### Set Upstream
```powershell
# During push
git push -u origin feature

# Or separately
git branch -u origin/feature

# Now just use:
git push  # Automatically pushes to upstream
git pull  # Automatically pulls from upstream
```

### Check Tracking
```powershell
git branch -vv
# Shows: [origin/main]  tracking info
```

## Common Workflows

### Daily Work
```powershell
# Start of day
git checkout main
git pull origin main

# Create feature
git checkout -b feature/xyz
# ... work ...
git commit -am "Work done"

# Push feature
git push -u origin feature/xyz

# After PR approved
git checkout main
git pull origin main
git branch -d feature/xyz
```

### Sync Fork
```powershell
# Update from upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

### Keep Feature Branch Updated
```powershell
git checkout feature
git fetch origin
git rebase origin/main
# Or:
git merge origin/main
```

## Verification

✅ You should now understand:
- Cloning repositories
- Adding/removing remotes
- Fetch vs pull
- Pushing changes
- Remote branches
- Upstream tracking
- Multiple remotes

## Pro Tips

### SSH vs HTTPS
```powershell
# HTTPS (requires password/token)
git clone https://github.com/user/repo.git

# SSH (requires SSH key setup)
git clone git@github.com:user/repo.git

# Convert HTTPS to SSH
git remote set-url origin git@github.com:user/repo.git
```

### Prune Deleted Remote Branches
```powershell
# Remote branch was deleted
git fetch --prune
# Or:
git remote prune origin
```

### View All Remote Branches
```powershell
git branch -r
git ls-remote origin
```

### Push All Branches
```powershell
git push --all origin
```

### Force Push (Dangerous!)
```powershell
# Only use after rebase on personal branch
git push --force origin feature
# Better:
git push --force-with-lease origin feature
```

---

**Exercise completed!** ✅  
You now understand remote repositories!
