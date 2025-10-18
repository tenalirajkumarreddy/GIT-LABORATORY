# Exercise 03: Working Directory Operations - Managing Changes

## 🎯 Objective
Learn to manage changes in your working directory: discard changes, restore files, and understand the difference between staged and unstaged modifications.

## 📚 Concepts Covered
- `git restore` - Discard changes
- `git checkout --` - Old way to discard changes
- Restoring specific files vs all files
- Understanding destructive operations
- The difference between working directory and staging area

## 📝 Preparation

```powershell
mkdir working-directory-practice
cd working-directory-practice
git init

# Create initial files
"# Shopping List App" | Out-File README.md -Encoding UTF8
"const items = [];" | Out-File app.js -Encoding UTF8
".env`nnode_modules/" | Out-File .gitignore -Encoding UTF8
git add .
git commit -m "Initial commit: Shopping list app"
```

## 📝 Tasks

### Task 1: Discard Unstaged Changes in a Single File
1. Modify `app.js` by adding: `const newFeature = 'coming soon';`
2. Check the status (should show modified)
3. View the diff to see your changes
4. Discard the changes using `git restore app.js`
5. Verify the file is back to original state
6. Check status (should be clean)

### Task 2: Discard All Unstaged Changes
1. Modify `app.js`: Add `console.log('Test 1');`
2. Modify `README.md`: Add `## Features` at the end
3. Create a new file `temp.txt` with any content
4. Check status (should show 2 modified, 1 untracked)
5. Discard changes in ALL tracked files using `git restore .`
6. Check status (temp.txt should still be untracked, others restored)
7. Delete temp.txt manually

### Task 3: Restore Deleted File
1. Delete `app.js` file using `Remove-Item app.js`
2. Check status (should show deleted)
3. Restore the deleted file using `git restore app.js`
4. Verify file is back
5. Check status (should be clean)

### Task 4: Restore Specific Version from History
1. Modify `README.md`: Add multiple lines:
   ```
   ## Installation
   npm install
   
   ## Usage
   npm start
   ```
2. Stage and commit: "docs: Add installation and usage"
3. Modify `README.md` again: Add `## License: MIT`
4. Stage and commit: "docs: Add license"
5. Now restore README.md to the state before "license" commit
6. Use: `git restore --source=HEAD~1 README.md`
7. Check the file (license section should be gone)
8. Check status (file should show as modified)

### Task 5: Understanding Restore vs Reset
1. Create a new file `feature.js` with content: `function test() {}`
2. Stage it: `git add feature.js`
3. Check status (should be staged)
4. Unstage it using: `git restore --staged feature.js`
5. Check status (now should be untracked, not staged)
6. Stage it again
7. Now discard it from working directory: `git restore feature.js`
8. Verify file still exists (because it was staged, Git knows about it)

### Task 6: The Dangerous Zone - Hard Discards
1. Modify `app.js`: Add several lines of "important" code
2. DON'T stage or commit
3. Check diff to see your changes
4. Discard these changes (they're gone forever!)
5. Try to recover (you can't - lesson learned!)

### Task 7: Selective File Restoration
1. Modify all three files (README.md, app.js, .gitignore)
2. Check status (all should be modified)
3. Restore only README.md, keep others modified
4. Verify README.md is restored but others are still modified
5. Now restore all remaining changes

### Task 8: Working with Staged and Unstaged Together
1. Modify `app.js`: Add line `// Stage 1`
2. Stage it: `git add app.js`
3. Modify `app.js` again: Add line `// Stage 2`
4. Check status (file shows in both staged and unstaged!)
5. Use `git restore --staged app.js` (unstages but keeps all changes)
6. Check status (now all changes are unstaged)
7. Use `git restore app.js` (discards all changes)

## ✅ Expected Outcome

You should understand:
- How to discard unwanted changes safely
- The difference between `--staged` and working directory restore
- How to recover deleted files
- How to restore files from any commit
- The destructive nature of discard operations
- How staging and working directory interact

## 🔍 Verification Commands

```powershell
# Should be clean
git status

# Should show all files exist
Get-ChildItem

# Should show no uncommitted changes
git diff
git diff --staged
```

## ⚠️ Important Warnings

**Discarding changes is PERMANENT!**
- Once you `git restore` a file, the changes are GONE
- There's no "undo" for discarded changes
- Always check `git diff` before discarding
- When in doubt, commit or stash instead

## 💡 Tips
- `git restore` is the new recommended way (Git 2.23+)
- `git checkout --` is the old way (still works)
- Use `--staged` to unstage, not to discard
- Use `--source=commit` to restore from specific commit
- Untracked files are not affected by `git restore .`

## 🆚 Restore vs Checkout vs Reset

```powershell
# Restore (new, recommended)
git restore file.txt              # Discard working directory changes
git restore --staged file.txt     # Unstage

# Checkout (old way)
git checkout -- file.txt          # Discard working directory changes

# Reset (different purpose)
git reset HEAD file.txt           # Unstage
git reset --hard                  # Discard everything (dangerous!)
```

## ⏭️ Next Exercise
Move on to **Exercise 04: Staging Area Mastery** to master the staging area!

---

**Time to Complete**: 20 minutes  
**Difficulty**: ⭐ Beginner  
**Prerequisites**: Exercise 01-02
