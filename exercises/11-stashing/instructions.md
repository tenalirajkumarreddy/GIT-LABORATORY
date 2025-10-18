# Exercise 11: Stashing - Temporary Storage

## 🎯 Objective
Master git stash to save work temporarily without committing, switch contexts, and manage multiple stashes.

## 📚 Concepts Covered
- Basic stashing
- Stashing with messages
- Listing and applying stashes
- Stash pop vs apply
- Partial stashing
- Creating branches from stashes
- Cleaning stashes

## 📝 Preparation

```powershell
mkdir stash-practice
cd stash-practice
git init

# Create initial files
"const app = 'main';" | Out-File app.js -Encoding UTF8
"# My Project" | Out-File README.md -Encoding UTF8
git add .
git commit -m "Initial commit"
```

## 📝 Tasks

### Task 1: Basic Stash
1. Modify app.js: Add line `console.log('Work in progress');`
2. Check status (should show modified)
3. Stash changes: `git stash`
4. Check status (clean!)
5. Check app.js (change is gone!)
6. Apply stash: `git stash apply`
7. Check app.js (change is back!)
8. Check stash list: `git stash list` (stash still there)

### Task 2: Stash with Message
1. Modify app.js: Add `const feature = 'auth';`
2. Stash with descriptive message: `git stash save "WIP: authentication feature"`
3. List stashes: `git stash list`
4. Notice the message makes it identifiable
5. Don't apply yet - we'll use it later

### Task 3: Stash Pop vs Apply
1. Modify README.md: Add `## Features`
2. Stash it: `git stash`
3. Apply with pop: `git stash pop`
4. List stashes: `git stash list`
5. Notice stash was REMOVED after pop
6. Difference:
   - `apply`: Keeps stash in list
   - `pop`: Removes stash from list after applying

### Task 4: Multiple Stashes
1. Modify app.js: Add line 1
2. Stash: `git stash save "Feature 1"`
3. Modify app.js: Add different line 2
4. Stash: `git stash save "Feature 2"`
5. Modify app.js: Add different line 3
6. Stash: `git stash save "Feature 3"`
7. List all stashes: `git stash list`
8. Notice they're numbered: stash@{0}, stash@{1}, stash@{2}

### Task 5: Apply Specific Stash
1. View stash list
2. Apply the middle stash: `git stash apply stash@{1}`
3. Check app.js - see those changes
4. Commit or discard
5. Apply another specific stash: `git stash apply stash@{2}`

### Task 6: View Stash Contents
1. List stashes: `git stash list`
2. Show stash contents: `git stash show stash@{0}`
3. Show detailed diff: `git stash show -p stash@{0}`
4. This helps you remember what's in each stash

### Task 7: Stash Untracked Files
1. Create new file: `"Untracked" | Out-File new.js -Encoding UTF8`
2. Try normal stash: `git stash`
3. Check if new.js exists (it still does!)
4. Stash with untracked: `git stash -u` (or `--include-untracked`)
5. Now new.js is stashed
6. Pop to restore: `git stash pop`

### Task 8: Stash Everything (Including Ignored)
1. Create .gitignore: `"*.log" | Out-File .gitignore -Encoding UTF8`
2. Create ignored file: `"Log" | Out-File test.log -Encoding UTF8`
3. Modify app.js
4. Stash all including ignored: `git stash --all`
5. Everything is stashed (even .gitignore and test.log)
6. Apply: `git stash apply`

### Task 9: Partial Stashing (Interactive)
1. Modify multiple files:
   - Add line to app.js
   - Add line to README.md
2. Stash interactively: `git stash -p`
3. Git will ask about each change (y/n/q/a/d/?)
4. Choose to stash only some changes
5. Check status (unstashed changes remain)

### Task 10: Create Branch from Stash
1. Make changes and stash: `git stash save "New feature idea"`
2. Realize this should be a feature branch
3. Create branch from stash: `git stash branch feature/new-idea stash@{0}`
4. This creates branch, checks it out, applies stash, and drops it
5. Perfect for turning exploratory work into a branch!

### Task 11: Drop Specific Stash
1. List stashes: `git stash list`
2. Drop specific stash: `git stash drop stash@{1}`
3. List again - it's gone
4. Drop most recent: `git stash drop` (defaults to stash@{0})

### Task 12: Clear All Stashes
1. Create several stashes
2. List them: `git stash list`
3. Clear all at once: `git stash clear`
4. ⚠️ This is permanent!
5. List again - all gone

### Task 13: Real-World Scenario - Emergency Hotfix
1. You're working on feature-branch with uncommitted changes
2. Emergency: Must fix bug on main NOW
3. Stash your work: `git stash save "WIP: half done feature"`
4. Switch to main: `git checkout main`
5. Fix bug and commit
6. Switch back: `git checkout feature-branch`
7. Restore work: `git stash pop`
8. Continue working!

### Task 14: Stash with --keep-index
1. Make changes to app.js
2. Stage them: `git add app.js`
3. Make more changes to same file (don't stage)
4. Stash only unstaged: `git stash --keep-index`
5. Staged changes remain, unstaged are stashed
6. Useful for testing staged changes before commit

### Task 15: Viewing Stash Stats
1. Create a stash with multiple file changes
2. View summary: `git stash show`
3. View detailed stats: `git stash show --stat`
4. View full diff: `git stash show -p`

## ✅ Expected Outcome

You should know:
- How to save work temporarily
- How to manage multiple stashes
- Difference between pop and apply
- How to stash untracked files
- How to create branches from stashes
- Real-world use cases for stashing

## 🎓 Key Concepts

### What is a Stash?
- Temporary storage for dirty working directory
- Not a commit (doesn't show in git log)
- Saved in a stack (LIFO - Last In, First Out)
- Can have multiple stashes

### Stash Structure
```
stash@{0} ← Most recent stash (top of stack)
stash@{1}
stash@{2}
stash@{3} ← Oldest stash (bottom of stack)
```

### Pop vs Apply
```
git stash pop    = apply + drop (one command)
git stash apply  = just apply (stash remains)
```

## 🔍 Verification Commands

```powershell
# List all stashes
git stash list

# Show stash contents
git stash show
git stash show -p

# Check working directory
git status

# See if stash exists
git stash list | Measure-Object -Line
```

## 💡 Pro Tips

### Naming Stashes
```powershell
# Bad (no context)
git stash

# Good (descriptive)
git stash save "WIP: user authentication - OAuth integration"
```

### Quick Stash Workflow
```powershell
# Save work
git stash

# Switch branches, do other work
git checkout main
git pull
git checkout feature-branch

# Resume work
git stash pop
```

### Stash Options Summary
```powershell
git stash                  # Stash tracked files
git stash -u               # Include untracked
git stash --all            # Include ignored files
git stash -p               # Interactive (partial)
git stash --keep-index     # Keep staged changes
```

### Stash Best Practices
1. **Use descriptive messages**: You'll thank yourself later
2. **Don't let stashes pile up**: Apply or drop them
3. **Prefer committing over stashing**: Commits are safer
4. **Use stash for context switching**: Not long-term storage

## 🎯 What You Should Know Now

- ✅ When to use stash
- ✅ How to create and apply stashes
- ✅ Difference between pop and apply
- ✅ How to manage multiple stashes
- ✅ How to stash untracked files
- ✅ How to create branches from stashes
- ✅ Real-world stash workflows

## 📊 Stash Commands Cheatsheet

```powershell
# Create stash
git stash                          # Basic stash
git stash save "message"           # With message
git stash -u                       # Include untracked
git stash --all                    # Include ignored
git stash -p                       # Interactive

# View stashes
git stash list                     # List all
git stash show                     # Show latest
git stash show stash@{n}          # Show specific
git stash show -p                  # Show diff

# Apply stash
git stash apply                    # Apply latest
git stash apply stash@{n}         # Apply specific
git stash pop                      # Apply and drop
git stash pop stash@{n}           # Pop specific

# Manage stashes
git stash drop                     # Drop latest
git stash drop stash@{n}          # Drop specific
git stash clear                    # Drop all
git stash branch name              # Create branch

# Advanced
git stash --keep-index             # Keep staged
git stash -p                       # Partial stash
```

## 🐛 Common Mistakes

1. **Forgetting stashes exist**: Use `git stash list` regularly
2. **Not naming stashes**: Hard to identify later
3. **Using stash as long-term storage**: Use commits instead
4. **Stashing instead of committing**: Commits are safer
5. **Stash conflicts**: Can happen when applying old stashes

## 💼 Real-World Scenarios

### Scenario 1: Quick Context Switch
```powershell
# Working on feature
git stash save "WIP: form validation"
git checkout main
# Fix urgent bug
git commit -am "hotfix: critical bug"
git checkout feature
git stash pop
```

### Scenario 2: Test Clean State
```powershell
# Have local changes
git stash
# Test without changes
npm test
# If tests pass, changes aren't needed
git stash drop
```

### Scenario 3: Explore Idea
```powershell
# Try experimental approach
# Make changes
git stash save "Experimental: new architecture"
# Create proper branch for it
git stash branch experiment/new-architecture
```

## ⏭️ Next Exercise
Ready for **Exercise 12: Tagging** - marking important points in history!

---

**Time to Complete**: 30 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-10
