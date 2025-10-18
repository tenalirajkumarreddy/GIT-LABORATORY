# Solution: Exercise 02 - Viewing History

## Complete Command Sequence

### Task 1: Basic Log Viewing

```powershell
# 1. Complete commit history
git log
# Shows all commits with full details

# 2. One-line format
git log --oneline
# Output example:
# f9a8b7c fix: Add punctuation to greeting
# e6d5c4b chore: Add configuration file
# d3c2b1a docs: Update README with features
# c1b0a9f refactor: Use greet utility in app
# b9a8c7d feat: Add greet function
# a1b2c3d Initial commit: Add README and app.js

# 3. Last 3 commits only
git log -n 3
# or
git log -3

# 4. Visual graph
git log --oneline --graph --all
# Shows branch structure with lines

# 5. With file statistics
git log --stat
# Shows files changed and lines added/removed per commit
```

### Task 2: Formatted Log Output

```powershell
# 1. Custom format: hash and message only
git log --pretty=format:"%h %s"
# Output: f9a8b7c fix: Add punctuation to greeting

# 2. With author and date
git log --pretty=format:"%h %s - %an, %ad"
# Output: f9a8b7c fix: Add punctuation to greeting - Your Name, Fri Oct 18 10:30:00 2025

# 3. With relative dates
git log --pretty=format:"%h %s (%ar)" --date=relative
# Output: f9a8b7c fix: Add punctuation to greeting (2 minutes ago)

# 4. Compact graph with colors
git log --oneline --graph --all --decorate --color
# Shows colorful branch graph

# Alternative: predefined formats
git log --pretty=oneline
git log --pretty=short
git log --pretty=full
git log --pretty=fuller
```

### Task 3: Filtering History

```powershell
# 1. Commits by author (replace with your name)
git log --author="Your Name"
# or partial match
git log --author="raj"

# 2. Commits from last 24 hours
git log --since="24 hours ago"
# or
git log --since="1 day ago"
# or
git log --after="2025-10-17"

# 3. Commits that modified utils.js
git log -- utils.js
# or with details
git log -p -- utils.js

# 4. Commits with "feat" in message
git log --grep="feat"
# Case insensitive
git log --grep="feat" -i

# 5. Commits that added/removed "greet"
git log -S "greet"
# This is called "pickaxe" search
```

### Task 4: Examining Specific Commits

```powershell
# 1. Most recent commit details
git show HEAD
# or simply
git show

# 2. 3rd commit from HEAD
git show HEAD~2
# Remember: HEAD~2 means "2 commits before HEAD"

# 3. First (initial) commit
# Method 1: Get hash from log
git log --oneline | Select-Object -Last 1
# Then: git show <hash>

# Method 2: Use rev-list
git show $(git rev-list --max-parents=0 HEAD)

# 4. Only commit message of HEAD
git log -1 --pretty=format:"%s"
# or
git show -s --format=%s HEAD

# 5. Commit that added config.json
git log --diff-filter=A -- config.json
# A = Added, M = Modified, D = Deleted
```

### Task 5: Comparing Changes

```powershell
# 1. Difference between HEAD and HEAD~1
git diff HEAD~1 HEAD
# or
git diff HEAD~1
# Shows what changed in last commit

# 2. Difference between first and last commit
# Get first commit hash
$firstCommit = git rev-list --max-parents=0 HEAD
git diff $firstCommit HEAD

# 3. What changed in app.js specifically
$firstCommit = git rev-list --max-parents=0 HEAD
git diff $firstCommit HEAD -- app.js

# 4. Difference between HEAD~3 and HEAD~1
git diff HEAD~3 HEAD~1

# 5. Word-level diff
git diff --word-diff HEAD~1 HEAD
# or color words
git diff --color-words HEAD~1 HEAD
```

### Task 6: File History and Blame

```powershell
# 1. All commits that modified utils.js
git log --oneline -- utils.js
# Output:
# f9a8b7c fix: Add punctuation to greeting
# b9a8c7d feat: Add greet function

# 2. Diff of changes to utils.js over time
git log -p -- utils.js
# Shows actual diffs for each commit

# 3. Blame on utils.js
git blame utils.js
# Output format:
# abc12345 (Your Name 2025-10-18 10:20:30 1) function greet(name) {
# abc12345 (Your Name 2025-10-18 10:20:30 2)   return 'Hello, ' + name + '!';
# abc12345 (Your Name 2025-10-18 10:20:30 3) }

# More readable blame
git blame -L 1,3 utils.js
# Only lines 1-3

# 4. Commit that added first line of README
git log -S "# Project Documentation" --source --all -- README.md
# or
git log -p -S "# Project Documentation" -- README.md

# 5. Search for "greeting" in commits
git log --all --grep="greeting" -i
# or in actual code changes
git log -S "greeting" --all
```

### Task 7: Advanced Searches

```powershell
# 1. Commits that added/removed "console.log"
git log -S "console.log"
# With patches
git log -S "console.log" -p

# Alternative: regex search
git log -G "console\.log"

# 2. History of deleted file (example)
# If you had deleted a file:
git log -- path/to/deleted/file.txt
# or find when it was deleted
git log --diff-filter=D --summary

# 3. Commits in date range
git log --since="2025-10-18" --until="2025-10-19"
# or
git log --after="1 hour ago" --before="now"

# 4. Commits not on any branch (dangling commits)
git log --all --not --remotes --not --branches
# You won't have any in this exercise

# 5. Shortest unique hash prefix
git log --oneline --abbrev=4
# Default is 7 characters, this shows 4
# Git automatically uses shortest unique prefix

# To find minimum unique length
git log --pretty=format:"%h %s" --abbrev-commit
```

## 📊 Understanding the Output

### Git Log Format Placeholders

```
%H  - Commit hash (full)
%h  - Commit hash (abbreviated)
%T  - Tree hash
%t  - Tree hash (abbreviated)
%P  - Parent hashes
%p  - Parent hashes (abbreviated)
%an - Author name
%ae - Author email
%ad - Author date
%ar - Author date, relative
%cn - Committer name
%ce - Committer email
%cd - Committer date
%cr - Committer date, relative
%s  - Subject (commit message)
%b  - Body
%d  - Ref names (branches, tags)
```

### Git Diff Output Explained

```diff
diff --git a/utils.js b/utils.js
index abc1234..def5678 100644
--- a/utils.js      ← Old version
+++ b/utils.js      ← New version
@@ -1,3 +1,3 @@     ← Line numbers (old range, new range)
-function greet(name) { return 'Hello ' + name; }
+function greet(name) { return 'Hello, ' + name + '!'; }
 
 ← Lines starting with - are removed
 ← Lines starting with + are added
 ← Lines with neither are context
```

### Git Blame Output Explained

```
abc12345 (Author Name 2025-10-18 10:20:30 1) function greet(name) {
│        │            │                   │   │
│        │            │                   │   └─ Actual code line
│        │            │                   └───── Line number
│        │            └────────────────────── Date/time
│        └───────────────────────────────── Author
└─────────────────────────────────────────── Commit hash
```

## 🎓 Key Concepts Explained

### HEAD and Relative References

```
HEAD      = Current commit
HEAD~1    = Parent commit (one before)
HEAD~2    = Grandparent (two before)
HEAD~3    = Great-grandparent (three before)

HEAD^     = First parent (same as HEAD~1)
HEAD^2    = Second parent (used in merges)
HEAD^^    = First parent's first parent (same as HEAD~2)
```

### Diff Filters

```
--diff-filter=A   = Added files only
--diff-filter=M   = Modified files only
--diff-filter=D   = Deleted files only
--diff-filter=R   = Renamed files only
--diff-filter=C   = Copied files only
--diff-filter=T   = Type changed (file ↔ symlink)
--diff-filter=U   = Unmerged files
--diff-filter=X   = Unknown
--diff-filter=B   = Broken pairing
```

## 🔍 Useful Log Aliases

Add these to your Git config for faster access:

```powershell
# Pretty log
git config --global alias.lg "log --oneline --graph --all --decorate"

# Log with stats
git config --global alias.ls "log --stat --abbrev-commit"

# Log with patches
git config --global alias.lp "log -p"

# Short log
git config --global alias.sl "log --oneline -10"

# Now you can use:
git lg
git ls
git lp
git sl
```

## 💡 Pro Tips

### 1. Finding When a Bug Was Introduced
```powershell
# Find when "bug" text was introduced
git log -S "buggy code"

# Find when function was removed
git log -S "functionName" --diff-filter=D
```

### 2. Viewing File at Specific Commit
```powershell
# Show file contents at commit
git show HEAD~3:app.js

# Save file from specific commit
git show HEAD~3:app.js | Out-File old-app.js
```

### 3. Compare Between Tags
```powershell
# If you had tags
git log v1.0..v2.0
git diff v1.0 v2.0
```

### 4. Find Commits That Touched Multiple Files
```powershell
git log -- file1.js file2.js
```

### 5. Ignore Whitespace in Diff
```powershell
git diff -w         # Ignore all whitespace
git diff -b         # Ignore whitespace changes
```

## 🎯 What You Should Know Now

- ✅ How to view history in multiple formats
- ✅ How to filter commits by various criteria
- ✅ How to examine specific commits in detail
- ✅ How to compare any two points in history
- ✅ How to track changes to specific files
- ✅ How to use blame to find who wrote what
- ✅ How to search through commit history efficiently

## 📝 Common Log Commands Cheatsheet

```powershell
# Basic viewing
git log                           # Full history
git log --oneline                # Compact view
git log -n 5                     # Last 5 commits
git log --stat                   # With file stats
git log -p                       # With diffs

# Filtering
git log --author="name"          # By author
git log --since="1 week ago"     # By date
git log --grep="pattern"         # By message
git log -S "text"                # By content change
git log -- file.txt              # For specific file

# Formatting
git log --graph                  # With graph
git log --pretty=oneline         # One line
git log --pretty=format:"%h %s"  # Custom format

# Comparing
git diff HEAD~1                  # Last commit changes
git diff branch1..branch2        # Between branches
git diff --stat                  # Summary only
git diff --word-diff             # Word-level

# File specific
git blame file.txt               # Line-by-line history
git log -p -- file.txt          # File history with diffs
```

## ⏭️ Ready for Next Exercise?

If you can answer these questions, you're ready:
1. How do you see commits from the last week?
2. How do you find who wrote a specific line?
3. How do you compare two commits?
4. What does HEAD~3 mean?

**Next: Exercise 03 - Working Directory Operations**

---

**Time to Complete**: 25-30 minutes  
**Difficulty**: ⭐ Beginner  
**Prerequisites**: Exercise 01
