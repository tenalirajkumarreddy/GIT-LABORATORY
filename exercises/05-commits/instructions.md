# Exercise 05: Commit Messages & Amending - Writing History

## 🎯 Objective
Learn to write professional commit messages and fix commits using amend.

## 📚 Concepts Covered
- Commit message conventions
- Good vs bad commit messages
- Using `git commit --amend`
- Changing last commit message
- Adding forgotten files to last commit
- Professional commit formats

## 📝 Preparation

```powershell
mkdir commit-messages-practice
cd commit-messages-practice
git init

# Create initial file
"const app = 'v1';" | Out-File app.js -Encoding UTF8
git add app.js
git commit -m "initial commit"
```

## 📝 Tasks

### Task 1: Bad Commit Messages (What NOT to Do)
Create commits with BAD messages to see the problem:

```powershell
"const feature = 1;" | Out-File feature.js -Encoding UTF8
git add feature.js
git commit -m "stuff"  # BAD: Vague

"const user = {};" | Out-File user.js -Encoding UTF8
git add user.js
git commit -m "asdf"  # BAD: Meaningless

"const fix = 1;" | Out-File fix.js -Encoding UTF8
git add fix.js
git commit -m "fixed the thing"  # BAD: What thing?
```

View log: `git log --oneline`
See how unhelpful these messages are!

### Task 2: Good Commit Messages (Best Practices)
Reset and do it right:

```powershell
git reset --hard HEAD~3  # Remove bad commits

# Good message: Descriptive, present tense
"const feature = 1;" | Out-File feature.js -Encoding UTF8
git add feature.js
git commit -m "Add user authentication feature"

# Good message: Explains what and why
"const user = {};" | Out-File user.js -Encoding UTF8
git add user.js
git commit -m "Create user model for authentication"

# Good message: Specific about fix
"const fix = 1;" | Out-File fix.js -Encoding UTF8
git add fix.js
git commit -m "Fix login validation error for empty passwords"
```

View log: Much better!

### Task 3: Conventional Commit Format
Use industry-standard format:

```
<type>: <subject>

[optional body]

[optional footer]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting (no code change)
- `refactor`: Code restructure (no behavior change)
- `test`: Adding tests
- `chore`: Maintenance

Practice:
```powershell
"const api = {};" | Out-File api.js -Encoding UTF8
git add api.js
git commit -m "feat: Add REST API endpoints for user management"

"// TODO: Add validation" | Out-File -Append api.js -Encoding UTF8
git add api.js
git commit -m "docs: Add TODO comments for API validation"

# Refactor existing code
"const api = { improved: true };" | Out-File api.js -Encoding UTF8
git add api.js
git commit -m "refactor: Simplify API structure for better maintainability"
```

### Task 4: Multi-Line Commit Messages
For complex commits, use body and footer:

```powershell
"const complex = true;" | Out-File complex.js -Encoding UTF8
git add complex.js

# Open editor for multi-line message
git commit
# In editor, write:
```
```
feat: Add complex payment processing system

This commit introduces a new payment gateway integration
that supports multiple payment methods including credit
cards, PayPal, and cryptocurrency.

Changes include:
- Payment processor interface
- Multiple payment method handlers
- Transaction logging
- Error handling and retry logic

Closes #123
Breaking change: Requires new API keys in config
```

### Task 5: Amend Last Commit Message (Typo Fix)
Oops, typo in commit message!

```powershell
"const typo = 1;" | Out-File typo.js -Encoding UTF8
git add typo.js
git commit -m "Add user atuhentication"  # Typo: atuhentication

# Fix the typo
git commit --amend -m "Add user authentication"

# View log - message is fixed!
git log -1
```

### Task 6: Amend to Add Forgotten File
Committed but forgot a file!

```powershell
# Make commit
"const main = 1;" | Out-File main.js -Encoding UTF8
git add main.js
git commit -m "Add main module"

# Oh no! Forgot the helper file
"const helper = 1;" | Out-File helper.js -Encoding UTF8

# Add forgotten file to last commit
git add helper.js
git commit --amend --no-edit

# View last commit - includes both files!
git show --stat
```

### Task 7: Amend Without Changing Message
Just adding files, keeping message:

```powershell
"const config = {};" | Out-File config.js -Encoding UTF8
git add config.js
git commit -m "Add configuration system"

# Forgot to update README
"## Configuration" | Out-File -Append README.md -Encoding UTF8
git add README.md

# Amend without opening editor
git commit --amend --no-edit

git show --stat  # Shows both changes
```

### Task 8: Amend and Change Message
Fix files AND message:

```powershell
"const wrong = 1;" | Out-File test.js -Encoding UTF8
git add test.js
git commit -m "Add tets"  # Typo

# Fix the file content
"const correct = 1;" | Out-File test.js -Encoding UTF8
git add test.js

# Amend with new message
git commit --amend -m "Add test configuration"
```

### Task 9: Commit Message Template
Create a template for consistency:

```powershell
# Create template file
@"
# <type>: <subject> (Max 50 characters)
# |<----  Using a maximum of 50 characters  ---->|


# Body: Explain what and why (not how)
# |<----   Try To Limit Each Line to a Maximum Of 72 Characters   ---->|


# Footer: Issue references, breaking changes
# Example: Closes #123, Breaking change: API v1 deprecated

# Types: feat, fix, docs, style, refactor, test, chore
"@ | Out-File commit-template.txt -Encoding UTF8

# Configure Git to use it
git config commit.template commit-template.txt

# Now commits open with template
git commit  # See template in editor
```

### Task 10: Atomic Commits with Good Messages
Practice the full workflow:

```powershell
# Make multiple unrelated changes
"const feature1 = {};" | Out-File feature1.js -Encoding UTF8
"const feature2 = {};" | Out-File feature2.js -Encoding UTF8
"const bugfix = {};" | Out-File bugfix.js -Encoding UTF8

# Commit each separately with good messages
git add feature1.js
git commit -m "feat: Add shopping cart functionality"

git add feature2.js
git commit -m "feat: Add user wishlist feature"

git add bugfix.js
git commit -m "fix: Resolve price calculation rounding error"

# View clean history
git log --oneline
```

### Task 11: Descriptive Subject Lines
Practice writing clear subjects:

```powershell
# Bad subjects
❌ "update"
❌ "fix bug"
❌ "changes"
❌ "."

# Good subjects
✅ "Add email validation to registration form"
✅ "Fix memory leak in image upload handler"
✅ "Update README with Docker installation steps"
✅ "Remove deprecated API endpoints"
```

### Task 12: Imperative Mood
Write in imperative (command) form:

```powershell
# Imperative mood (Good)
✅ "Add feature"
✅ "Fix bug"
✅ "Update documentation"
✅ "Remove deprecated code"

# Past tense (Bad)
❌ "Added feature"
❌ "Fixed bug"
❌ "Updated documentation"

# Practice imperative commits
"const imp = 1;" | Out-File imp.js -Encoding UTF8
git add imp.js
git commit -m "Add imperial units converter"  # Not "Added"
```

### Task 13: Explain Why, Not How
The code shows HOW, commit explains WHY:

```powershell
# Bad: Describes how (code does this)
❌ "Change variable name from x to userCount"

# Good: Explains why (business reason)
✅ "Improve code readability for user counting logic"

# Practice
"const why = 'better performance';" | Out-File perf.js -Encoding UTF8
git add perf.js
git commit -m "Optimize database queries to reduce page load time"
```

### Task 14: Reference Issues/Tickets
Link commits to issues:

```powershell
"const issue = 1;" | Out-File issue.js -Encoding UTF8
git add issue.js
git commit -m "fix: Resolve login timeout issue

Users were experiencing timeouts during peak hours
due to connection pool exhaustion.

Fixes #456
Related to #123"
```

### Task 15: Breaking Changes
Mark breaking changes clearly:

```powershell
"const breaking = 'new';" | Out-File api.js -Encoding UTF8
git add api.js
git commit -m "feat: Migrate to REST API v2

BREAKING CHANGE: API v1 endpoints are deprecated.
All clients must update to v2 endpoints.

Migration guide: docs/migration-v2.md
Closes #789"
```

## ✅ Expected Outcome

You should know:
- How to write professional commit messages
- Conventional commit format
- How to use amend for fixes
- When to write detailed vs short messages
- How to create atomic commits with good messages

## 🎓 Key Concepts

### The 7 Rules of Great Commit Messages

1. **Separate subject from body** with a blank line
2. **Limit subject to 50 characters**
3. **Capitalize the subject line**
4. **Do not end subject with a period**
5. **Use imperative mood** ("Add" not "Added")
6. **Wrap body at 72 characters**
7. **Use body to explain** what and why (not how)

### Good Commit Message Template
```
<type>: <subject>
<blank line>
<body>
<blank line>
<footer>
```

### Amend vs New Commit

**Use Amend When:**
- Fixing typo in message
- Adding forgotten file
- Small fix to last commit
- Commit NOT pushed yet

**Create New Commit When:**
- Already pushed
- Unrelated change
- Significant time has passed

## 🔍 Verification Commands

```powershell
# View commit messages
git log
git log --oneline

# View last commit in detail
git show

# View commit message only
git log -1 --pretty=%B

# View commit with files
git show --stat
```

## 💡 Pro Tips

### Commit Message Editor
```powershell
# Set your preferred editor
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "notepad"      # Notepad
```

### Commit Message Tips
```powershell
# Quick one-liner
git commit -m "message"

# Open editor for longer message
git commit

# Amend last commit
git commit --amend

# Amend without changing message
git commit --amend --no-edit
```

### Check Before Commit
```powershell
# See what you're about to commit
git diff --staged

# Review changes
git status
```

## 🎯 What You Should Know Now

- ✅ How to write professional commit messages
- ✅ Conventional commit format (feat, fix, docs, etc.)
- ✅ How to use git commit --amend
- ✅ When to write detailed vs short messages
- ✅ The 7 rules of great commit messages
- ✅ How to fix commit mistakes

## 📊 Commit Message Examples

### Good Examples
```
feat: Add user registration with email verification

fix: Resolve database connection timeout in production

docs: Update API documentation for authentication endpoints

refactor: Extract payment processing into separate service

test: Add unit tests for shopping cart calculations

chore: Update dependencies to latest stable versions
```

### Bad Examples
```
update
fixed stuff
changes
working on feature
temp commit
asdf
.
```

## ⏭️ Next Exercise
Move on to **Exercise 06: Branching Basics** to learn Git's superpower!

---

**Time to Complete**: 30 minutes  
**Difficulty**: ⭐ Beginner  
**Prerequisites**: Exercise 01-04
