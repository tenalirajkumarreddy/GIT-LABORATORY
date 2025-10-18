# Exercise 02: Viewing History - Exploring Your Commits

## 🎯 Objective
Master all the ways to view and explore your Git history. Learn to use log, show, diff, and blame commands effectively.

## 📚 Concepts Covered
- `git log` with various options
- `git show` for detailed commit view
- `git diff` for comparing changes
- `git blame` for tracking line changes
- Searching through history

## 📝 Preparation

### Setup Your Practice Repository
```powershell
mkdir git-history-practice
cd git-history-practice
git init
```

Now create this history by running these commands:

```powershell
# Commit 1: Create initial files
"# Project Documentation" | Out-File README.md -Encoding UTF8
"console.log('Hello World');" | Out-File app.js -Encoding UTF8
git add .
git commit -m "Initial commit: Add README and app.js"

# Commit 2: Add feature
"function greet(name) { return 'Hello ' + name; }" | Out-File utils.js -Encoding UTF8
git add utils.js
git commit -m "feat: Add greet function"

# Commit 3: Update app to use utility
"const { greet } = require('./utils');`nconsole.log(greet('World'));" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "refactor: Use greet utility in app"

# Commit 4: Update README
"# Project Documentation`n`n## Features`n- Greeting function" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "docs: Update README with features"

# Commit 5: Add config
"{ `"version`": `"1.0.0`" }" | Out-File config.json -Encoding UTF8
git add config.json
git commit -m "chore: Add configuration file"

# Commit 6: Bug fix
"function greet(name) { return 'Hello, ' + name + '!'; }" | Out-File utils.js -Encoding UTF8
git add utils.js
git commit -m "fix: Add punctuation to greeting"
```

## 📝 Tasks

### Task 1: Basic Log Viewing
1. View the complete commit history
2. View the history in one-line format
3. View only the last 3 commits
4. View the history with a visual graph
5. View the history with file statistics (`--stat`)

### Task 2: Formatted Log Output
1. View log with custom format showing only hash and message
2. View log with author name and date
3. View log with relative dates (e.g., "2 hours ago")
4. View log in a compact graph format with colors

### Task 3: Filtering History
1. View commits by author (use your name)
2. View commits from the last 24 hours
3. View commits that modified `utils.js`
4. View commits with "feat" in the message
5. View commits that added or removed the word "greet"

### Task 4: Examining Specific Commits
1. Show the details of your most recent commit
2. Show what changed in the 3rd commit from HEAD
3. Show the details of the first (initial) commit
4. Display only the commit message of HEAD
5. Show the commit that added `config.json`

### Task 5: Comparing Changes
1. See the difference between HEAD and HEAD~1
2. See the difference between your first and last commit
3. See what changed in `app.js` between the first and last commit
4. See the difference between HEAD~3 and HEAD~1
5. Show word-level diff instead of line-level

### Task 6: File History and Blame
1. View all commits that modified `utils.js`
2. View the diff of changes made to `utils.js` over time
3. Use `git blame` on `utils.js` to see who wrote each line
4. View the commit that added the first line of `README.md`
5. Search for all commits that mention "greeting"

### Task 7: Advanced Searches
1. Find commits that added or removed "console.log"
2. View the commit history of a deleted file (if you deleted any)
3. Show commits in date range (use actual dates based on when you created)
4. Show all commits that are NOT on any branch (you won't have any, but try the command)
5. Show the shortest unique commit hash prefix

## ✅ Expected Outcome

You should be able to:
- View history in multiple formats
- Find specific commits quickly
- Compare any two points in history
- Track changes to specific files
- Understand blame output
- Search commit messages and content

## 🔍 Verification Commands

```powershell
# Should show 6 commits
git log --oneline | Measure-Object -Line

# Should show visual graph
git log --oneline --graph --all

# Should show statistics
git log --stat

# Should show only certain commits
git log --author="Your Name"

# Should show differences
git diff HEAD~5 HEAD
```

## 💡 Tips
- `HEAD` means the current commit
- `HEAD~1` means one commit before HEAD
- `HEAD~3` means three commits before HEAD
- You can use commit hashes instead of HEAD~n
- Use `--oneline` for quick overview, full log for details

## ⏭️ Next Exercise
Move on to **Exercise 03: Working Directory Operations** to learn how to manage changes in your working directory.

---

**Time to Complete**: 20-25 minutes  
**Difficulty**: ⭐ Beginner  
**Prerequisites**: Exercise 01
