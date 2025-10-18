# Exercise 19: Real-World Scenario - Wrong Branch Commit

## 🎯 Objective
Learn to recover from one of the most common Git mistakes: committing work to the wrong branch.

## 📚 Concepts Covered
- Moving commits between branches
- Cherry-picking
- Resetting branches
- Branch management
- Multiple recovery strategies

## 🎭 The Scenario

You're working on a team project. You have:
- `main` branch (production)
- `develop` branch (integration)
- You should create `feature/user-profile` branch

**The Mistake**: You forgot to create feature branch and made 3 commits directly to `main`!

Now you need to:
1. Move those commits to the correct feature branch
2. Clean up main branch
3. Do it without losing any work

## 📝 Preparation

```powershell
mkdir wrong-branch-rescue
cd wrong-branch-rescue
git init

# Setup main branch
"v1.0" | Out-File app.js -Encoding UTF8
"# Project" | Out-File README.md -Encoding UTF8
git add .
git commit -m "Initial release v1.0"

# Create develop branch
git checkout -b develop
"dev config" | Out-File config.js -Encoding UTF8
git add config.js
git commit -m "Add dev config"

# Switch to main (simulating you think you're on feature branch)
git checkout main
```

## 📝 Tasks

### Task 1: Make the Mistake (Commit to Wrong Branch)
1. You THINK you're on feature/user-profile, but actually on main
2. Check current branch: `git branch` (shows * main - oh no!)
3. But you don't notice and create commits:
   ```powershell
   # Commit 1: Start user profile
   "function getUserProfile() { }" | Out-File profile.js -Encoding UTF8
   git add profile.js
   git commit -m "feat: Add user profile function"
   
   # Commit 2: Add profile picture
   "function addProfilePicture() { }" | Out-File -Append profile.js -Encoding UTF8
   git commit -am "feat: Add profile picture upload"
   
   # Commit 3: Add bio
   "function updateBio() { }" | Out-File -Append profile.js -Encoding UTF8
   git commit -am "feat: Add user bio field"
   ```
4. Now you realize: "Oh no! I'm on main, not feature branch!" 😱

### Task 2: Assess the Damage
1. View git log: `git log --oneline`
2. See 3 wrong commits on main
3. View branches: `git branch -a`
4. No feature/user-profile branch exists
5. Check if you pushed: `git log origin/main..main` (if no remote, skip)
6. Good news: Not pushed yet! Can fix locally.

### Task 3: Strategy 1 - Cherry-Pick Method
This is the SAFEST method:

1. First, note the hashes of the 3 wrong commits
2. Create the correct branch FROM the point before mistakes:
   ```powershell
   git branch feature/user-profile HEAD~3
   ```
3. Verify feature branch has correct starting point:
   ```powershell
   git log feature/user-profile --oneline
   ```
4. Cherry-pick the commits to correct branch:
   ```powershell
   git checkout feature/user-profile
   git cherry-pick main~2  # Profile function
   git cherry-pick main~1  # Profile picture
   git cherry-pick main    # User bio
   ```
5. Now remove commits from main:
   ```powershell
   git checkout main
   git reset --hard HEAD~3
   ```
6. Verify both branches:
   ```powershell
   git log --oneline --graph --all
   ```

### Task 4: Strategy 2 - Branch Move Method
Alternative approach (reset task 3 to try this):

```powershell
# Reset to try another method
git checkout main
git reset --hard HEAD~3
git branch -D feature/user-profile

# Redo the wrong commits
"function getUserProfile() { }" | Out-File profile.js -Encoding UTF8
git add profile.js
git commit -m "feat: Add user profile function"
"function addProfilePicture() { }" | Out-File -Append profile.js -Encoding UTF8
git commit -am "feat: Add profile picture upload"
"function updateBio() { }" | Out-File -Append profile.js -Encoding UTF8
git commit -am "feat: Add user bio field"
```

Now use branch move:
1. Create feature branch at current position:
   ```powershell
   git branch feature/user-profile
   ```
2. Move main back 3 commits:
   ```powershell
   git reset --hard HEAD~3
   ```
3. Switch to feature branch:
   ```powershell
   git checkout feature/user-profile
   ```
4. Verify: All commits are there!

### Task 5: Strategy 3 - Using Reflog (If Already Reset)
Simulate losing commits:

1. Reset main: `git reset --hard HEAD~3`
2. Oh no! Forgot to create branch first!
3. Commits seem lost... but reflog saves us!
4. View reflog: `git reflog`
5. Find the commit before reset
6. Create branch there:
   ```powershell
   git branch feature/user-profile HEAD@{1}
   ```
7. Or reset to it:
   ```powershell
   git reset --hard HEAD@{1}
   git branch feature/user-profile
   git reset --hard HEAD~3
   ```

### Task 6: What If You Already Pushed?

Simulate pushed commits:
```powershell
# Add a fake remote
git remote add origin fake://repo.git
```

Now the problem is worse - commits are public!

**Don't force push shared branches!** Instead:

1. **Option A**: Revert the commits on main:
   ```powershell
   git revert HEAD~2..HEAD
   ```
2. **Option B**: If others haven't pulled:
   - Communicate with team
   - Force push carefully:
   ```powershell
   git push --force-with-lease
   ```

### Task 7: Complex Scenario - Mixed Commits
Create a harder scenario:

1. Reset everything
2. Make valid main commits and wrong commits mixed:
   ```powershell
   git checkout main
   echo "hotfix" | Out-File hotfix.js; git add hotfix.js; git commit -m "hotfix: Critical bug"
   echo "feature" | Out-File wrong1.js; git add wrong1.js; git commit -m "feat: Wrong 1"
   echo "hotfix2" | Out-File hotfix2.js; git add hotfix2.js; git commit -m "hotfix: Another fix"
   echo "feature" | Out-File wrong2.js; git add wrong2.js; git commit -m "feat: Wrong 2"
   ```
3. Need to cherry-pick only the feature commits
4. Solution:
   ```powershell
   git checkout -b feature/extracted
   git cherry-pick <wrong1-hash>
   git cherry-pick <wrong2-hash>
   git checkout main
   # Can't simply reset - would lose hotfixes!
   # Use interactive rebase instead
   git rebase -i HEAD~4
   # Mark wrong commits as 'drop'
   ```

### Task 8: Prevention - Set Up Safeguards
Prevent future mistakes:

1. Configure Git to show branch in prompt
2. Use pre-commit hook to warn on main:
   ```powershell
   # In .git/hooks/pre-commit
   $branch = git branch --show-current
   if ($branch -eq "main" -or $branch -eq "master") {
     Write-Host "⚠️  WARNING: Committing to $branch!" -ForegroundColor Yellow
     Write-Host "Are you sure? Press Ctrl+C to cancel"
     Start-Sleep -Seconds 3
   }
   ```
3. Use Git aliases for safer workflow:
   ```powershell
   git config --global alias.newfeature '!git checkout -b'
   ```

### Task 9: Team Communication
If you pushed to shared branch:

1. Immediately communicate with team:
   - Slack/Teams message
   - Tell them NOT to pull
2. Document what happened
3. Share your fix
4. Learn from mistake

### Task 10: Practice All Methods
Reset and practice each recovery method multiple times until comfortable:

1. Cherry-pick method
2. Branch move method
3. Reflog recovery
4. Interactive rebase for selective removal

## ✅ Expected Outcome

You should know:
- How to move commits between branches
- Multiple recovery strategies
- When to use each method
- How to handle pushed mistakes
- Prevention strategies

## 🎓 Key Concepts

### Recovery Methods Comparison

| Method | Pros | Cons | When to Use |
|--------|------|------|-------------|
| Cherry-pick | Safest, preserves originals | More steps | When unsure |
| Branch Move | Quick, simple | Need to know commit count | When confident |
| Reflog | Can recover anything | Need to find right commit | After mistakes |
| Interactive Rebase | Selective removal | Complex | Mixed commits |

### Safety Levels
```
Safest     → Cherry-pick → Create branch → Reset
           → If pushed → Revert
Dangerous  → Force push (team coordination required)
```

## 💡 Pro Tips

### Check Branch Before Committing
```powershell
# Always verify current branch
git branch --show-current

# Or full status
git status
```

### Quick Branch Creation Alias
```powershell
# Create and checkout in one command
git config --global alias.cob 'checkout -b'

# Use it
git cob feature/new-feature
```

### Visual Branch Indicator
Use PowerShell prompt to show Git branch:
```powershell
# In your PowerShell profile
function prompt {
  $branch = git branch --show-current 2>$null
  if ($branch) {
    "[$branch] PS $($executionContext.SessionState.Path.CurrentLocation)> "
  } else {
    "PS $($executionContext.SessionState.Path.CurrentLocation)> "
  }
}
```

### Prevent Direct Commits to Main
```powershell
# Use branch protection (GitHub/GitLab)
# Or pre-commit hook to warn
```

## 🎯 What You Should Know Now

- ✅ How to recover from wrong branch commits
- ✅ Multiple recovery strategies
- ✅ Cherry-pick vs branch move vs reflog
- ✅ How to handle pushed mistakes
- ✅ Prevention techniques
- ✅ When to use force push safely

## 📊 Decision Tree

```
Committed to wrong branch?
│
├─ Not pushed yet?
│  ├─ Simple case (all commits wrong)?
│  │  └─ Use Branch Move Method ✅
│  └─ Complex case (mixed commits)?
│     └─ Use Cherry-pick or Interactive Rebase ✅
│
└─ Already pushed?
   ├─ Others pulled?
   │  └─ Use Revert ✅ (safest)
   └─ Nobody pulled yet?
      ├─ Coordinate with team
      └─ Force push with --force-with-lease ⚠️
```

## 🐛 Common Mistakes

1. **Force pushing without communication**: Always tell team first
2. **Not checking reflog**: Can recover almost anything
3. **Panicking and making it worse**: Stay calm, assess first
4. **Not backing up**: Create backup branch before fixing
5. **Using --force instead of --force-with-lease**: Safer option exists

## 💼 Real-World Examples

### Example 1: Startup Developer
```
Problem: 5 commits to main instead of feature branch
Solution: Branch move method
Result: Clean main, feature branch created
```

### Example 2: Open Source Contributor
```
Problem: PR commits to main instead of fork branch
Solution: Cherry-pick to new branch, force push fork
Result: Proper PR created
```

### Example 3: Enterprise Team
```
Problem: Pushed to protected main (bypassed protection)
Solution: Revert commits, create proper PR workflow
Result: History preserved, proper review added
```

## 🎪 Bonus Challenge

Create this complex scenario and solve it:
1. 10 commits on main
2. Commits 2, 5, 7 should be on feature-a
3. Commits 3, 6, 9 should be on feature-b
4. Rest should stay on main

Solution involves:
- Interactive rebase
- Multiple cherry-picks
- Careful commit tracking

## ⏭️ Next Exercise
Ready for **Exercise 20: History Cleanup** - advanced history manipulation!

---

**Time to Complete**: 30 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: Exercise 01-18
**Real-World Relevance**: ⭐⭐⭐⭐⭐ (Everyone makes this mistake!)
