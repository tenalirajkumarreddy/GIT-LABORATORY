# Exercise 08: Merge Conflicts - Resolution Mastery

## 🎯 Objective
Master the art of resolving merge conflicts - one of the most important real-world Git skills.

## 📚 Concepts Covered
- Understanding why conflicts occur
- Reading conflict markers
- Resolving conflicts manually
- Using merge tools
- Aborting merges
- Preventing conflicts

## 📝 Preparation

```powershell
mkdir conflict-resolution
cd conflict-resolution
git init

# Create base file
@"
# Team Collaboration Tool

## Version: 1.0.0

## Features
- Task management
- User authentication

## Team
- Project lead
"@ | Out-File README.md -Encoding UTF8

git add README.md
git commit -m "Initial project documentation"
```

## 📝 Tasks

### Task 1: Create Your First Conflict
1. Create branch `feature/add-chat`
2. Switch to it
3. Modify README.md - change "Version: 1.0.0" to "Version: 1.1.0"
4. Add under Features: "- Real-time chat"
5. Commit: "Add chat feature and bump version"
6. Switch back to main
7. Modify README.md - change "Version: 1.0.0" to "Version: 2.0.0"
8. Add under Features: "- File sharing"
9. Commit: "Add file sharing and bump version"
10. Try to merge feature/add-chat: `git merge feature/add-chat`
11. CONFLICT! 🔥

### Task 2: Understanding the Conflict
1. Run `git status` - see conflicted files
2. Open README.md in editor
3. Look for conflict markers:
   ```
   <<<<<<< HEAD
   ## Version: 2.0.0
   =======
   ## Version: 1.1.0
   >>>>>>> feature/add-chat
   ```
4. Understand:
   - `<<<<<<< HEAD`: Your current branch changes
   - `=======`: Separator
   - `>>>>>>> feature/add-chat`: Incoming branch changes

### Task 3: Resolve the Conflict Manually
1. Open README.md
2. Decide what to keep:
   - Keep version 2.0.0 (main)
   - Keep BOTH features (chat and file sharing)
3. Edit file to remove conflict markers and combine changes:
   ```
   ## Version: 2.0.0
   
   ## Features
   - Task management
   - User authentication
   - Real-time chat
   - File sharing
   ```
4. Save the file
5. Stage the resolved file: `git add README.md`
6. Check status (should say "all conflicts fixed")
7. Complete merge: `git commit` (message auto-generated)
8. View log graph to see merge commit

### Task 4: Multiple File Conflicts
1. Create file `config.js`:
   ```javascript
   const config = {
     port: 3000,
     database: 'mongodb'
   };
   ```
2. Commit: "Add configuration"
3. Create branch `feature/postgres`
4. Modify config.js: change database to 'postgresql'
5. Commit: "Switch to PostgreSQL"
6. Switch to main
7. Modify config.js: change port to 8080
8. Commit: "Change port to 8080"
9. Merge feature/postgres - CONFLICT!
10. Resolve by keeping both changes:
    ```javascript
    const config = {
      port: 8080,
      database: 'postgresql'
    };
    ```
11. Complete merge

### Task 5: Abort a Merge
1. Create branch `feature/experimental`
2. Make conflicting changes
3. Try to merge
4. Realize the conflict is too complex
5. Abort the merge: `git merge --abort`
6. Verify you're back to pre-merge state
7. Branch is still there, merge never happened

### Task 6: Complex Conflict with Context
1. Create file `users.js`:
   ```javascript
   function getUser(id) {
     return database.findById(id);
   }
   
   function createUser(data) {
     return database.insert(data);
   }
   
   function deleteUser(id) {
     return database.delete(id);
   }
   ```
2. Commit: "Add user functions"
3. Create branch `feature/async`
4. Modify to add async/await:
   ```javascript
   async function getUser(id) {
     return await database.findById(id);
   }
   ```
5. Commit: "Make getUser async"
6. Switch to main
7. Modify same function differently:
   ```javascript
   function getUser(id, options = {}) {
     return database.findById(id, options);
   }
   ```
8. Commit: "Add options parameter"
9. Merge feature/async - CONFLICT!
10. Resolve by combining BOTH changes:
    ```javascript
    async function getUser(id, options = {}) {
      return await database.findById(id, options);
    }
    ```
11. Complete merge

### Task 7: Viewing Conflict Diffs
1. Create another conflict (any way)
2. Use `git diff` to see conflicts
3. Use `git diff --ours` to see main version
4. Use `git diff --theirs` to see feature branch version
5. Use `git diff --base` to see common ancestor
6. These help understand what changed where

### Task 8: Taking One Side Entirely
1. Create branch `feature/complete-rewrite`
2. Completely rewrite a file
3. Switch to main and modify same file
4. Merge - CONFLICT!
5. Decide to keep their version entirely:
   `git checkout --theirs filename`
6. Or keep ours entirely:
   `git checkout --ours filename`
7. Stage and commit

### Task 9: Conflict Resolution Strategy
1. Create branch `feature/refactor`
2. Make significant changes
3. Switch to main, make different changes
4. Before merging, check what would conflict:
   - `git merge --no-commit --no-ff feature/refactor`
5. See conflicts in advance
6. Abort if needed: `git merge --abort`

### Task 10: Practice Real-World Scenario
1. Simulate team work:
   - You work on `feature/your-work`
   - Teammate's work simulated on main
2. Both modify same functions
3. Both add new functions in same area
4. Merge and resolve thoughtfully
5. Test that code still works logically
6. Complete merge with good message

## ✅ Expected Outcome

You should be able to:
- Recognize when conflicts will occur
- Read and understand conflict markers
- Resolve conflicts intelligently
- Use various merge strategies
- Abort merges when needed
- Prevent future conflicts

## 🎓 Key Concepts

### Conflict Markers Explained
```
<<<<<<< HEAD (Your current branch)
Your changes here
||||||| merged common ancestors (with diff3)
Original code
=======
Their changes here
>>>>>>> branch-name (Incoming branch)
```

### Why Conflicts Happen
1. Same line modified differently in both branches
2. One branch modifies, other deletes
3. Both branches add different content at same location
4. File renamed in one branch, modified in other

### Conflict Resolution Strategies
1. **Keep ours**: `git checkout --ours file`
2. **Keep theirs**: `git checkout --theirs file`
3. **Manual merge**: Edit file yourself
4. **Abort**: `git merge --abort`

## 🔍 Verification Commands

```powershell
# See conflicted files
git status

# See conflict diff
git diff

# See what we have
git diff --ours

# See what they have
git diff --theirs

# List unmerged files
git diff --name-only --diff-filter=U

# Check merge in progress
git status | Select-String "merge"
```

## 💡 Pro Tips

### Prevention is Better Than Cure
```powershell
# Pull often
git pull origin main

# Keep branches short-lived
# Merge frequently to main

# Communicate with team
# Know who's working on what files

# Use smaller commits
# Easier to resolve conflicts
```

### VS Code Conflict Resolution
VS Code shows helpful buttons above conflicts:
- Accept Current Change (ours)
- Accept Incoming Change (theirs)
- Accept Both Changes
- Compare Changes

### Testing After Resolution
```powershell
# ALWAYS test after resolving conflicts
# - Does code still run?
# - Are there syntax errors?
# - Do tests pass?
# - Does logic make sense?
```

## 🎯 What You Should Know Now

- ✅ Why conflicts occur
- ✅ How to read conflict markers
- ✅ Multiple resolution strategies
- ✅ When to abort a merge
- ✅ How to use --ours and --theirs
- ✅ How to prevent conflicts
- ✅ Best practices for conflict resolution

## 🐛 Common Mistakes

1. **Keeping conflict markers**: Always remove `<<<<<<<`, `=======`, `>>>>>>>`
2. **Not testing after resolution**: Code might not work!
3. **Taking one side blindly**: Understand WHAT changed
4. **Forgetting to git add**: Must stage resolved files
5. **Complex merges in one go**: Break into smaller merges

## 📋 Conflict Resolution Checklist

- [ ] Understand why conflict occurred
- [ ] Read both versions carefully
- [ ] Decide on resolution strategy
- [ ] Edit file to remove markers
- [ ] Ensure code is syntactically correct
- [ ] Stage resolved files
- [ ] Test if possible
- [ ] Complete merge commit
- [ ] Verify with git log

## ⏭️ Next Exercise
Ready for **Exercise 09: Remote Repositories** - collaborating with others!

---

**Time to Complete**: 35-40 minutes  
**Difficulty**: ⭐⭐ Intermediate  
**Prerequisites**: Exercise 01-07
