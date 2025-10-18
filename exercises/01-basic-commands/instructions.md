# Exercise 01: Git Basics - Your First Repository

## 🎯 Objective
Learn to initialize a repository, stage files, and create your first commits. Understand the three trees concept.

## 📚 Concepts Covered
- `git init` - Initialize a repository
- `git status` - Check repository status
- `git add` - Stage files
- `git commit` - Save snapshots
- Understanding Working Directory → Staging Area → Repository flow

## 📝 Tasks

### Task 1: Initialize a Git Repository
1. Create a new folder called `my-first-repo` in this directory
2. Navigate into the folder
3. Initialize it as a Git repository
4. Check the status

### Task 2: Create and Stage Files
1. Create a file called `welcome.txt` with the content: "Hello Git! This is my first repository."
2. Create another file called `about.txt` with the content: "Learning Git is fun!"
3. Check the status (notice the untracked files)
4. Stage only the `welcome.txt` file
5. Check the status again (notice the difference)
6. Stage the `about.txt` file
7. Check the status one more time

### Task 3: Make Your First Commit
1. Commit the staged files with the message: "Initial commit: Add welcome and about files"
2. Check the status (should be clean)
3. View the commit history

### Task 4: Make More Changes
1. Modify `welcome.txt` by adding a new line: "I'm learning version control!"
2. Create a new file called `goals.txt` with content: "Goal: Master Git in 30 days"
3. Check the status (notice modified vs untracked)
4. Stage only the modified `welcome.txt`
5. Check the status (see what's staged vs not staged)
6. Now stage `goals.txt` as well
7. Commit with message: "Add goals and update welcome message"

### Task 5: Understanding the Three Trees
1. Create a new file `test.txt` with any content
2. Stage it using `git add`
3. Modify the same file again (add another line)
4. Check the status carefully - notice how the same file appears in both staged and unstaged!
5. Use `git diff` to see unstaged changes
6. Use `git diff --staged` to see staged changes
7. Stage the new changes
8. Commit with message: "Add test file"

### Task 6: Viewing Your Work
1. View the complete commit history
2. View the compact one-line log
3. View the last 2 commits only
4. Show details of your most recent commit

## ✅ Expected Outcome

After completing this exercise, you should have:
- A Git repository with `.git` folder
- 3 commits in your history
- 4 files: `welcome.txt`, `about.txt`, `goals.txt`, `test.txt`
- Understanding of the three trees (Working Directory, Staging Area, Repository)

## 🎓 What You Learned

- How Git tracks changes through three stages
- The difference between untracked, modified, and staged files
- How to create atomic commits
- How to view your repository's history

## 🔍 Verification Commands

```powershell
# Should show 3 commits
git log --oneline

# Should show clean working tree
git status

# Should show 4 files
Get-ChildItem

# Should show all commits with details
git log
```

## 💡 Tips
- Use `git status` after EVERY command while learning
- Commit messages should be descriptive and present tense
- The staging area lets you choose exactly what to commit
- You can stage parts of changes, not just whole files

## ⏭️ Next Exercise
Once you're comfortable with these basics, move on to **Exercise 02: Viewing History** to learn more about exploring your commits.
