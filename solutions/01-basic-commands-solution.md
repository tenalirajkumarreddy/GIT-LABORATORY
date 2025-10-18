# Solution: Exercise 01 - Git Basics

## Complete Command Sequence

### Task 1: Initialize a Git Repository
```powershell
# Create and navigate to folder
mkdir my-first-repo
cd my-first-repo

# Initialize Git repository
git init
# Output: Initialized empty Git repository in .../.git/

# Check status
git status
# Output: On branch main (or master)
#         No commits yet
#         nothing to commit
```

### Task 2: Create and Stage Files
```powershell
# Create welcome.txt
"Hello Git! This is my first repository." | Out-File -FilePath welcome.txt -Encoding UTF8

# Create about.txt
"Learning Git is fun!" | Out-File -FilePath about.txt -Encoding UTF8

# Check status
git status
# Output: Untracked files:
#         welcome.txt
#         about.txt

# Stage only welcome.txt
git add welcome.txt

# Check status again
git status
# Output: Changes to be committed:
#         new file: welcome.txt
#         Untracked files:
#         about.txt

# Stage about.txt
git add about.txt

# Check status
git status
# Output: Changes to be committed:
#         new file: welcome.txt
#         new file: about.txt
```

### Task 3: Make Your First Commit
```powershell
# Commit staged files
git commit -m "Initial commit: Add welcome and about files"
# Output: [main (root-commit) abc1234] Initial commit: Add welcome and about files
#         2 files changed, 2 insertions(+)

# Check status
git status
# Output: On branch main
#         nothing to commit, working tree clean

# View history
git log
# Shows commit hash, author, date, and message
```

### Task 4: Make More Changes
```powershell
# Modify welcome.txt (append new line)
"I'm learning version control!" | Out-File -FilePath welcome.txt -Append -Encoding UTF8

# Create goals.txt
"Goal: Master Git in 30 days" | Out-File -FilePath goals.txt -Encoding UTF8

# Check status
git status
# Output: Changes not staged for commit:
#         modified: welcome.txt
#         Untracked files:
#         goals.txt

# Stage welcome.txt
git add welcome.txt

# Check status
git status
# Output: Changes to be committed:
#         modified: welcome.txt
#         Untracked files:
#         goals.txt

# Stage goals.txt
git add goals.txt

# Commit
git commit -m "Add goals and update welcome message"
```

### Task 5: Understanding the Three Trees
```powershell
# Create test.txt
"This is a test" | Out-File -FilePath test.txt -Encoding UTF8

# Stage it
git add test.txt

# Modify it again
"Adding another line" | Out-File -FilePath test.txt -Append -Encoding UTF8

# Check status - IMPORTANT!
git status
# Output: Changes to be committed:
#         new file: test.txt
#         Changes not staged for commit:
#         modified: test.txt

# See unstaged changes
git diff
# Shows the line you just added

# See staged changes
git diff --staged
# Shows the original content that's staged

# Stage the new changes
git add test.txt

# Commit
git commit -m "Add test file"
```

### Task 6: Viewing Your Work
```powershell
# Complete history
git log

# Compact one-line log
git log --oneline
# Output: abc1234 Add test file
#         def5678 Add goals and update welcome message
#         ghi9012 Initial commit: Add welcome and about files

# Last 2 commits only
git log -n 2

# Details of most recent commit
git show HEAD
# or
git show
```

## 📊 Final Repository State

```
my-first-repo/
├── .git/           (Git's database)
├── welcome.txt     (Modified in commit 2)
├── about.txt       (Added in commit 1)
├── goals.txt       (Added in commit 2)
└── test.txt        (Added in commit 3)
```

## 🎓 Key Concepts Explained

### The Three Trees in Action

**Scenario from Task 5:**
```
Working Directory (test.txt with 2 lines)
         ↑
         | git diff (shows line 2)
         |
Staging Area (test.txt with 1 line)
         ↑
         | git diff --staged (shows line 1)
         |
Repository (.git directory)
```

This demonstrates that:
- A file can exist in different states across all three trees
- `git diff` compares Working Directory ← → Staging Area
- `git diff --staged` compares Staging Area ← → Repository
- This is why you can have same file in "staged" AND "not staged" sections

### Understanding Git Status Output

```
On branch main
Changes to be committed:           ← STAGING AREA (Green)
  new file: test.txt

Changes not staged for commit:    ← WORKING DIRECTORY (Red)
  modified: test.txt

Untracked files:                   ← WORKING DIRECTORY (Red)
  newfile.txt
```

## 🔍 Verification

Run these commands to verify your completion:

```powershell
# Should show 3 commits
git log --oneline | Measure-Object -Line
# Count should be 3

# Should show 4 files (excluding .git)
Get-ChildItem -File | Measure-Object
# Count should be 4

# Should show clean status
git status
# Should see: "nothing to commit, working tree clean"

# Should show proper history
git log --oneline --all --graph
```

## 💡 Additional Tips

### Best Practices Demonstrated
1. **Atomic Commits**: Each commit represents one logical change
2. **Descriptive Messages**: Clear commit messages help future you
3. **Frequent Status Checks**: Always know what state your repo is in
4. **Selective Staging**: Stage only what belongs in the next commit

### Common Mistakes to Avoid
- ❌ `git add .` without checking what you're staging
- ❌ Vague commit messages like "fixed stuff"
- ❌ Not using `git status` frequently
- ❌ Committing broken code

### PowerShell Tips
```powershell
# Create file with content
"content" | Out-File -FilePath file.txt -Encoding UTF8

# Append to file
"more content" | Out-File -FilePath file.txt -Append -Encoding UTF8

# View file
Get-Content file.txt
# or
cat file.txt
```

## 🎯 What You Should Know Now

- ✅ How to initialize a Git repository
- ✅ The three-stage process: Working → Staging → Repository
- ✅ How to stage and commit files
- ✅ How to view repository history
- ✅ The difference between `git diff` and `git diff --staged`
- ✅ That files can exist in different states simultaneously

## 🐛 Troubleshooting

### If you made a mistake:
```powershell
# Start over completely
cd ..
Remove-Item -Recurse -Force my-first-repo
# Then repeat the exercise
```

### If commits are wrong:
```powershell
# Undo last commit, keep changes
git reset --soft HEAD~1

# Undo last commit, discard changes
git reset --hard HEAD~1
```

## ⏭️ Ready for Next Exercise?

If you can complete this exercise without looking at the solution, you're ready for **Exercise 02: Viewing History**!

---

**Time to Complete**: 15-20 minutes  
**Difficulty**: ⭐ Beginner  
**Prerequisites**: None
