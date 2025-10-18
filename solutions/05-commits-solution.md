# Solution: Exercise 05 - Commits

## Complete Command Sequence

```powershell
# Preparation
mkdir commit-practice
cd commit-practice
git init

# Task 1: Bad commit message
"Code" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "stuff"
# ❌ Not descriptive!

# Task 2: Good commit message
"Feature code" | Out-File feature.js -Encoding UTF8
git add feature.js
git commit -m "feat: add user authentication feature

- Implement login functionality
- Add password hashing
- Create session management"
# ✅ Descriptive and detailed!

# Task 3: Conventional commits
"Component" | Out-File component.js -Encoding UTF8
git add component.js
git commit -m "feat: add new component"

"Bug fix" | Out-File fix.js -Encoding UTF8
git add fix.js
git commit -m "fix: resolve null pointer exception"

"Documentation" | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "docs: update README with setup instructions"

# Task 4: View commit history
git log
git log --oneline
git log --graph --oneline

# Task 5: Amend last commit (message)
git commit --amend -m "docs: update README with complete setup guide"
git log --oneline  # Last commit message changed

# Task 6: Amend last commit (add file)
"Forgot this" | Out-File forgotten.js -Encoding UTF8
git add forgotten.js
git commit --amend --no-edit
# File added to last commit without changing message

# Task 7: Commit with -a flag
"Tracked file" | Out-File tracked.js -Encoding UTF8
git add tracked.js
git commit -m "Add tracked file"
"Modified" | Out-File tracked.js -Encoding UTF8
git commit -am "Update tracked file"
# Stages and commits tracked files in one command

# Task 8: Write good commit messages
git commit -m "feat: implement password reset functionality

Adds email-based password reset flow with:
- Email template for reset link
- Token generation and validation
- Expiration after 1 hour

Closes #123"

# Task 9: Multiline commit messages
git commit -m "fix: resolve login timeout issue" -m "Users were experiencing timeouts after 30 seconds. Increased timeout to 5 minutes and added retry logic."

# Task 10: Empty commit
git commit --allow-empty -m "chore: trigger CI/CD pipeline"

# Task 11: Commit part of changes
"Feature 1" | Out-File multi.js -Encoding UTF8
"Debug code" | Add-Content multi.js
git add -p multi.js
# Stage only Feature 1, not debug code
git commit -m "feat: add feature 1"

# Task 12: View commit details
git show HEAD
git show HEAD~1
git show <commit-hash>

# Task 13: Sign commits (if GPG configured)
# git commit -S -m "feat: signed commit"
# Requires GPG key setup

# Task 14: Reference issues
git commit -m "fix: resolve memory leak

Fixes #456
Related to #123, #789"

# Task 15: Commit message template
@"
# Title: <type>: <subject>
# |<----  50 chars  ---->|

# Explain why this change is being made
# |<----   Wrap at 72 chars   -->|

# Provide links to any relevant tickets, articles or resources
"@ | Out-File ../.gitmessage -Encoding UTF8

git config commit.template ../.gitmessage
git commit  # Opens template
```

## Conventional Commit Types

```
feat:     New feature
fix:      Bug fix
docs:     Documentation only
style:    Formatting, missing semicolons, etc.
refactor: Code change that neither fixes bug nor adds feature
perf:     Performance improvement
test:     Adding tests
build:    Build system changes
ci:       CI configuration changes
chore:    Other changes (updating dependencies, etc.)
```

## Good Commit Message Format

```
<type>: <subject>

<body>

<footer>
```

### Example:
```
feat: add user profile page

- Create profile component
- Add avatar upload
- Implement bio editing
- Add privacy settings

Closes #234
```

## Seven Rules of Good Commit Messages

1. **Separate subject from body with blank line**
2. **Limit subject line to 50 characters**
3. **Capitalize the subject line**
4. **Don't end subject line with period**
5. **Use imperative mood** ("Add feature" not "Added feature")
6. **Wrap body at 72 characters**
7. **Use body to explain what and why, not how**

## Amending Commits

### Change Last Commit Message
```powershell
git commit --amend -m "New message"
```

### Add Forgotten Files
```powershell
git add forgotten-file.txt
git commit --amend --no-edit
```

### Change Both
```powershell
git add file.txt
git commit --amend -m "Updated message"
```

## Verification

✅ You should now be able to:
- Write clear, descriptive commit messages
- Use conventional commit format
- Amend commits
- Create atomic commits
- Reference issues in commits

## Common Mistakes

### ❌ Bad Commit Messages
```
git commit -m "fix"
git commit -m "WIP"
git commit -m "asdf"
git commit -m "final version"
git commit -m "oops"
```

### ✅ Good Commit Messages
```
git commit -m "fix: resolve null pointer in user service"
git commit -m "feat: implement dark mode toggle"
git commit -m "docs: add API documentation"
git commit -m "refactor: extract helper functions"
```

## Pro Tips

### Template for Commit Messages
```powershell
# Set global template
git config --global commit.template ~/.gitmessage

# Set for this repo only
git config commit.template .gitmessage
```

### Quick Amend
```powershell
# Stage changes and amend in one command
git commit -a --amend --no-edit
```

### View Commit Stats
```powershell
git show --stat HEAD
git log --oneline --stat
```

---

**Exercise completed!** ✅  
You now write professional commit messages!
