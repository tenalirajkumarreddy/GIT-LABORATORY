# Solution: Exercise 03 - Working Directory

## Complete Command Sequence

```powershell
# Preparation
mkdir working-directory-practice
cd working-directory-practice
git init

# Task 1: View working directory status
git status
# Shows clean working directory

# Task 2: Create untracked file
"Hello Git" | Out-File newfile.txt -Encoding UTF8
git status
# Shows newfile.txt as untracked

# Task 3: Modify tracked file
"Initial content" | Out-File tracked.txt -Encoding UTF8
git add tracked.txt
git commit -m "Add tracked file"
"Modified content" | Out-File tracked.txt -Encoding UTF8
git status
# Shows tracked.txt as modified

# Task 4: View differences
git diff
# Shows changes in working directory vs staging area

# Task 5: Stage some changes
git add tracked.txt
git status
# Shows tracked.txt in "Changes to be committed"

# Task 6: Make more changes after staging
"More changes" | Out-File tracked.txt -Encoding UTF8
git status
# Shows tracked.txt in BOTH staged and unstaged sections!

# Task 7: View staged vs unstaged
git diff           # Unstaged changes
git diff --staged  # Staged changes

# Task 8: Discard unstaged changes
git restore tracked.txt
git status
# Working directory clean

# Task 9: Unstage files
"New content" | Out-File tracked.txt -Encoding UTF8
git add tracked.txt
git restore --staged tracked.txt
git status
# File is modified but not staged

# Task 10: Multiple file states
"File A" | Out-File fileA.txt -Encoding UTF8
"File B" | Out-File fileB.txt -Encoding UTF8
"File C" | Out-File fileC.txt -Encoding UTF8
git add fileA.txt
git add fileB.txt
git commit -m "Add files A and B"
"Modified A" | Out-File fileA.txt -Encoding UTF8
"Modified B" | Out-File fileB.txt -Encoding UTF8
git add fileA.txt
git status
# fileA: staged
# fileB: modified (not staged)
# fileC: untracked

# Task 11: Clean untracked files
git clean -n   # Dry run (shows what would be deleted)
git clean -f   # Actually delete
git status
# Untracked files removed

# Task 12: Ignore files
".env
*.log
node_modules/
dist/" | Out-File .gitignore -Encoding UTF8
git add .gitignore
git commit -m "Add .gitignore"
".env file" | Out-File .env -Encoding UTF8
git status
# .env is not shown (ignored)

# Task 13: Working directory vs index vs HEAD
"WD content" | Out-File test.txt -Encoding UTF8
git add test.txt
git commit -m "Commit test.txt"
"Modified" | Out-File test.txt -Encoding UTF8
git add test.txt
"Modified again" | Out-File test.txt -Encoding UTF8
git diff HEAD      # WD vs HEAD (all changes)
git diff --staged  # Staged vs HEAD
git diff           # WD vs Staged

# Task 14: Discard all changes
git restore .
git status
# All unstaged changes discarded

# Task 15: Complete state check
git status
git status -s  # Short format
git status --porcelain  # Machine-readable format
```

## Key Concepts Demonstrated

### Three Areas
1. **Working Directory**: Your actual files
2. **Staging Area (Index)**: Prepared changes
3. **Repository (HEAD)**: Committed changes

### File States
- **Untracked**: New files not in Git
- **Unmodified**: Files unchanged since last commit
- **Modified**: Changed but not staged
- **Staged**: Ready to be committed

### Important Commands
```powershell
git status          # Check status
git diff            # Working vs Staged
git diff --staged   # Staged vs HEAD
git diff HEAD       # Working vs HEAD
git restore <file>  # Discard changes
git restore --staged <file>  # Unstage
git clean -f        # Remove untracked files
```

## Common Patterns

### Check Before Committing
```powershell
git status              # What's changed?
git diff                # See unstaged changes
git diff --staged       # See staged changes
git add <files>         # Stage what you want
git commit -m "message" # Commit
```

### Undo Mistakes
```powershell
# Undo unstaged changes
git restore <file>

# Unstage files
git restore --staged <file>

# Remove untracked files
git clean -n  # Preview
git clean -f  # Do it
```

### Ignore Files
```powershell
# Add to .gitignore
echo ".env" >> .gitignore
echo "*.log" >> .gitignore

# Commit .gitignore
git add .gitignore
git commit -m "Add gitignore"
```

## Verification

After completing all tasks, you should be able to:
- ✅ Understand working directory vs staging area vs repository
- ✅ View file states with `git status`
- ✅ Use `git diff` variants
- ✅ Stage and unstage files
- ✅ Discard unwanted changes
- ✅ Clean untracked files
- ✅ Use .gitignore

## Troubleshooting

**Q: `git clean` won't delete my files**  
A: Use `git clean -f` (force flag required)

**Q: File shows as modified after `git restore`**  
A: Check line endings (CRLF vs LF) or permissions

**Q: Can't discard changes to file**  
A: Use `git restore <file>` (not `git checkout` in Git 2.23+)

**Q: How to see what `git clean` would delete?**  
A: Use `git clean -n` for dry run

---

**Exercise completed!** ✅  
You now understand the working directory and how to manage file states in Git.
