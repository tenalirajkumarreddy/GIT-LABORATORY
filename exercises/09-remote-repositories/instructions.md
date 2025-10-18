# Exercise 09: Remote Repositories - Collaboration Basics

## 🎯 Objective
Learn to work with remote repositories (GitHub, GitLab) including cloning, pushing, pulling, and fetching.

## 📚 Concepts Covered
- Understanding remotes
- `git clone` - Copy repository
- `git remote` - Manage remotes
- `git push` - Send changes
- `git pull` - Get changes
- `git fetch` - Download without merging
- Tracking branches

## 📝 Preparation

Since we can't actually create a GitHub repo in this exercise, we'll simulate remote operations using local "bare" repositories.

```powershell
mkdir remote-practice
cd remote-practice

# Create a "remote" repository (simulated)
git init --bare remote-repo.git

# Create local repository
mkdir local-repo
cd local-repo
git init
```

## 📝 Tasks

### Task 1: Add a Remote
1. Check current remotes: `git remote -v` (empty)
2. Add remote: `git remote add origin ../remote-repo.git`
3. Check again: `git remote -v`
4. See origin listed twice (fetch and push)

### Task 2: View Remote Information
1. List remotes: `git remote`
2. Verbose list: `git remote -v`
3. Detailed info: `git remote show origin`

### Task 3: First Push
1. Create initial commit:
   ```powershell
   "# Project" | Out-File README.md -Encoding UTF8
   git add README.md
   git commit -m "Initial commit"
   ```
2. Push to remote: `git push origin main`
3. This fails! No upstream set
4. Push with upstream: `git push -u origin main`
5. `-u` sets tracking relationship

### Task 4: Understanding Tracking Branches
1. After `git push -u`, check: `git branch -vv`
2. See [origin/main] tracking info
3. Now can use: `git push` (without specifying branch)
4. Also: `git pull` works automatically

### Task 5: Clone a Repository
1. Navigate out: `cd ../..`
2. Clone the "remote": `git clone remote-practice/remote-repo.git cloned-repo`
3. Enter cloned repo: `cd cloned-repo`
4. Check remotes: `git remote -v` (origin automatically set)
5. View files: See README.md

### Task 6: Make Changes and Push
1. In cloned-repo, modify README:
   ```powershell
   "## Features" | Out-File -Append README.md -Encoding UTF8
   git add README.md
   git commit -m "Add features section"
   ```
2. Push changes: `git push`
3. Changes are in "remote" now

### Task 7: Pull Changes
1. Go back to original repo: `cd ../remote-practice/local-repo`
2. Pull updates: `git pull origin main`
3. See the new features section in README!
4. Your local repo is now up to date

### Task 8: Fetch vs Pull
1. In cloned-repo, make another change:
   ```powershell
   cd ../../cloned-repo
   "## Installation" | Out-File -Append README.md -Encoding UTF8
   git commit -am "Add installation section"
   git push
   ```
2. In local-repo, fetch (don't merge): 
   ```powershell
   cd ../remote-practice/local-repo
   git fetch origin
   ```
3. Check log: `git log origin/main` (see new commit)
4. Your branch: `git log main` (doesn't have it yet)
5. Now merge: `git merge origin/main`
6. Understanding: Fetch downloads, pull = fetch + merge

### Task 9: Push Conflicts
1. Create conflict scenario:
   - In local-repo: Modify line 1 of README, commit
   - In cloned-repo: Modify line 1 differently, commit, push
2. In local-repo: Try to push
3. Push rejected! Remote has changes
4. Must pull first: `git pull`
5. Resolve conflict
6. Then push

### Task 10: Multiple Remotes
1. Create second "remote":
   ```powershell
   cd ../..
   git init --bare backup-repo.git
   ```
2. In local-repo, add second remote:
   ```powershell
   cd remote-practice/local-repo
   git remote add backup ../../backup-repo.git
   ```
3. Check remotes: `git remote -v`
4. Push to both:
   ```powershell
   git push origin main
   git push backup main
   ```

### Task 11: Rename Remote
1. Rename origin: `git remote rename origin upstream`
2. Check: `git remote -v`
3. Now use: `git push upstream main`

### Task 12: Change Remote URL
1. View current URL: `git remote get-url upstream`
2. Change it: `git remote set-url upstream ../../new-location.git`
3. View again: URL changed

### Task 13: Remove Remote
1. Add temp remote: `git remote add temp ../temp.git`
2. List: `git remote -v`
3. Remove: `git remote remove temp`
4. Check: Gone!

### Task 14: View Remote Branches
1. Create branch in cloned-repo:
   ```powershell
   cd ../../cloned-repo
   git checkout -b feature
   "Feature code" | Out-File feature.js -Encoding UTF8
   git add feature.js
   git commit -m "Add feature"
   git push -u origin feature
   ```
2. In local-repo, fetch:
   ```powershell
   cd ../remote-practice/local-repo
   git fetch upstream
   ```
3. View remote branches: `git branch -r`
4. See origin/feature

### Task 15: Track Remote Branch
1. Create local branch tracking remote:
   ```powershell
   git checkout -b feature upstream/feature
   ```
2. Or shorter: `git checkout feature` (Git figures it out)
3. Check tracking: `git branch -vv`
4. Now can push/pull to feature branch

## ✅ Expected Outcome

You should understand:
- What remotes are
- How to clone repositories
- Difference between push and pull
- Difference between fetch and pull
- How to resolve push conflicts
- Tracking branches

## 🎓 Key Concepts

### Remote Repository
- A version of your repository hosted elsewhere
- Can be GitHub, GitLab, BitBucket, or even another folder
- Multiple people can access same remote
- Central collaboration point

### Common Remote Names
- **origin**: Default name for cloned repository's source
- **upstream**: Often used for original repo when you fork
- **backup**: Sometimes used for backup location

### Fetch vs Pull
```
fetch: Download changes (don't merge)
pull:  Download and merge (fetch + merge)
```

### Push Requirements
- Must have commits to push
- Must pull first if remote has new commits
- Must resolve conflicts before pushing

## 🔍 Verification Commands

```powershell
# List remotes
git remote -v

# See remote info
git remote show origin

# Check tracking
git branch -vv

# View remote branches
git branch -r

# View all branches
git branch -a
```

## 💡 Pro Tips

### First Time Setup
```powershell
# After creating repo on GitHub
git remote add origin https://github.com/user/repo.git
git branch -M main
git push -u origin main
```

### Common Workflow
```powershell
# Start of day
git pull

# Make changes
git add .
git commit -m "message"

# End of day
git push
```

### Before Pushing
```powershell
# Always check status
git status

# Pull first to avoid conflicts
git pull

# Then push
git push
```

## 🎯 What You Should Know Now

- ✅ How to add and manage remotes
- ✅ How to clone repositories
- ✅ How to push changes
- ✅ How to pull changes
- ✅ Difference between fetch and pull
- ✅ How to resolve push conflicts
- ✅ How tracking branches work

## 📊 Remote Commands Cheatsheet

```powershell
# Add remote
git remote add origin <url>

# Clone repository
git clone <url>

# Push changes
git push origin main
git push -u origin main  # Set upstream
git push                 # After upstream set

# Pull changes
git pull origin main
git pull                 # After upstream set

# Fetch only
git fetch origin

# List remotes
git remote -v

# Remote info
git remote show origin

# Rename remote
git remote rename old new

# Change URL
git remote set-url origin <new-url>

# Remove remote
git remote remove origin
```

## 🌐 Real GitHub Workflow

When you actually use GitHub:

```powershell
# 1. Create repo on GitHub
# 2. Clone it
git clone https://github.com/username/repo.git

# 3. Make changes
git add .
git commit -m "message"

# 4. Push
git push

# 5. Pull others' changes
git pull

# Repeat 3-5
```

## ⏭️ Next Exercise
Move on to **Exercise 10: Undoing Changes** to learn recovery techniques!

---

**Time to Complete**: 35 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-08
