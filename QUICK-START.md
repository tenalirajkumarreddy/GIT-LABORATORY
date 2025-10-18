# Quick Start Guide

## 🚀 Getting Started with Git Practice Exercises

### Prerequisites
- Git installed on your system
- VS Code or any text editor
- PowerShell terminal (Windows)
- Basic command line knowledge

### Installation Check
```powershell
# Verify Git is installed
git --version
# Should show: git version 2.x.x
```

### Initial Setup (REQUIRED)
Before starting any exercises, configure Git:

```powershell
# Set your identity (REQUIRED)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Better output
git config --global color.ui auto

# Verify setup
git config --list
```

---

## 📚 How to Use This Repository

### For Complete Beginners
Start here - don't skip!

```powershell
# 1. Navigate to first exercise
cd "exercises/01-basic-commands"

# 2. Read instructions
cat instructions.md
# OR open in VS Code
code instructions.md

# 3. Complete the tasks

# 4. Check solution when done
cat "../../solutions/01-basic-commands-solution.md"

# 5. Move to next exercise
cd "../02-viewing-history"
```

### For Experienced Users
Choose your path:

```powershell
# Know commits but not branching?
cd "exercises/06-branching-basics"

# Know branching but not rebasing?
cd "exercises/14-rebasing"

# Just want practice scenarios?
cd "exercises/19-scenario-wrong-branch"
```

---

## 📋 Recommended Learning Paths

### Path 1: Beginner (2 hours)
Focus on fundamentals
```
01-basic-commands
02-viewing-history
03-working-directory
04-staging-area
05-commits
```

### Path 2: Collaboration Ready (4 hours)
Can work with teams
```
01-05 (from Path 1)
06-branching-basics
07-merging-branches
08-merge-conflicts
09-remote-repositories
```

### Path 3: Professional (7 hours)
Complete intermediate skills
```
01-12 (all beginner + intermediate)
```

### Path 4: Complete Mastery (10-12 hours)
Become a Git expert
```
01-20 (all exercises)
```

---

## 🎯 Your First Exercise

Let's complete Exercise 01 together!

### Step 1: Navigate to Exercise
```powershell
cd "exercises/01-basic-commands"
```

### Step 2: Read Instructions
```powershell
# View instructions
cat instructions.md

# Or open in editor
code instructions.md
```

### Step 3: Create Practice Folder
```powershell
# Create folder for practice
mkdir my-first-repo
cd my-first-repo
```

### Step 4: Initialize Git
```powershell
# Make it a Git repository
git init

# Check status
git status
```

### Step 5: Create Your First Commit
```powershell
# Create a file
"Hello Git!" | Out-File welcome.txt -Encoding UTF8

# Check status (see untracked file)
git status

# Stage the file
git add welcome.txt

# Check status (see staged file)
git status

# Commit it
git commit -m "My first commit"

# Check status (clean working tree)
git status

# View history
git log
```

### 🎉 Congratulations!
You've completed your first Git operations!

---

## 💡 Essential Commands to Memorize

### The Big 5 (Use Every Day)
```powershell
git status      # Check what's happening
git add .       # Stage all changes
git commit -m   # Save snapshot
git log         # View history
git push        # Send to remote
```

### The Safety Net
```powershell
git status      # Always check first!
git diff        # See what changed
git log         # Know where you are
```

---

## 🆘 Stuck? Start Here

### Can't remember what to do?
```powershell
git status
# Git tells you what's happening and suggests commands
```

### Made a mistake?
```powershell
# For unstaged changes
git restore filename

# For staged changes
git restore --staged filename

# For committed changes (not pushed)
git reset --soft HEAD~1
```

### Completely lost?
```powershell
# Start exercise over
cd ..
Remove-Item -Recurse -Force practice-folder
# Read instructions again
```

---

## 📚 Exercise Structure Explained

Each exercise folder contains:

```
exercises/XX-topic-name/
├── instructions.md       ← What to do
└── starter-files/        ← Files to use (some exercises)

solutions/
└── XX-topic-solution.md  ← How to do it (check after trying)
```

### Instructions File Contains
- 🎯 Learning objectives
- 📚 Concepts covered
- 📝 Step-by-step tasks
- ✅ Expected outcomes
- 💡 Pro tips

### Solution File Contains
- Complete command sequences
- Detailed explanations
- Common mistakes to avoid
- Verification steps
- Additional resources

---

## ⚠️ Important Rules

### 1. Try Before Looking at Solutions
```
❌ Read solution first
✅ Try exercise → Get stuck → Check solution
```

### 2. Use git status Constantly
```powershell
git status  # After EVERY command while learning
```

### 3. Experiment Freely
```
Each exercise is independent
Can't break anything important
If lost, start over!
```

### 4. Take Breaks
```
20-30 minutes per exercise
Break after every 2-3 exercises
Practice over multiple days
```

---

## 🎓 Tips for Success

### Create Cheat Sheet
As you learn, note commands:
```powershell
# My Git Cheat Sheet
git status              # Check status
git add .              # Stage all
git commit -m "msg"    # Commit
git log --oneline      # View history
# ... add more as you learn
```

### Use Git Help
```powershell
# Get help for any command
git help <command>
git <command> --help

# Quick reference
git <command> -h
```

### Practice Daily
```
Even 15 minutes helps
Repetition builds muscle memory
Better to practice than read
```

---

## 📊 Track Your Progress

Use the checklist in `EXERCISE-INDEX.md`:

```markdown
- [x] Exercise 01: Git Basics ✅
- [x] Exercise 02: Viewing History ✅
- [ ] Exercise 03: Working Directory
- [ ] Exercise 04: Staging Area
...
```

---

## 🎯 First Week Goals

### Day 1: Setup & Basics
- Configure Git
- Complete Exercise 01
- Understand three trees

### Day 2: Viewing & Managing
- Complete Exercise 02
- Complete Exercise 03
- Practice git status constantly

### Day 3: Staging & Commits
- Complete Exercise 04
- Complete Exercise 05
- Write good commit messages

### Day 4: Branching
- Complete Exercise 06
- Understand branches as pointers
- Practice switching branches

### Day 5: Merging
- Complete Exercise 07
- Understand fast-forward vs three-way
- Complete Exercise 08 (conflicts)

### Weekend: Review
- Redo exercises that were confusing
- Practice without looking at solutions
- Create real project to apply skills

---

## 🔧 Troubleshooting

### Git Not Found
```powershell
# Install Git from: https://git-scm.com/download/win
# Restart terminal after installation
```

### Permission Errors
```powershell
# Run as administrator if needed
# Or check folder permissions
```

### Merge Conflicts Scary
```
Don't panic!
Conflicts are normal
Exercise 08 teaches resolution
Everyone struggles at first
```

### Lost or Confused
```powershell
# Start exercise over
cd ..
Remove-Item -Recurse -Force practice-folder

# Or ask for help in README issues
```

---

## 🎉 Next Steps

Once you're comfortable with first exercise:

1. **Continue linearly**: Do exercises in order
2. **Check EXERCISE-INDEX.md**: See all exercises
3. **Join community**: Share progress, ask questions
4. **Apply to real projects**: Best way to learn!

---

## 📚 Additional Resources

### Official Git Resources
- [Pro Git Book](https://git-scm.com/book) - Free, comprehensive
- [Git Documentation](https://git-scm.com/doc) - Official docs
- [Git Reference](https://git-scm.com/docs) - Command reference

### Interactive Learning
- [Learn Git Branching](https://learngitbranching.js.org/) - Visual, interactive
- [Git Immersion](http://gitimmersion.com/) - Guided tour

### Video Tutorials
- Search YouTube for "Git tutorial for beginners"
- GitHub's own YouTube channel

---

## ✅ Ready to Start?

```powershell
# Let's begin!
cd "exercises/01-basic-commands"
cat instructions.md

# Good luck! 🚀
```

---

## 🆘 Need Help?

- **Stuck on exercise**: Check solution file
- **Concept unclear**: Reread that section in instructions
- **Made mistake**: Start exercise over (it's okay!)
- **Want to contribute**: Open issue or PR in GitHub

**Remember**: Everyone struggles with Git at first. Keep practicing! 💪

---

**Happy Learning!** 🎓
