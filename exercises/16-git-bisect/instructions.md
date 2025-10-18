# Exercise 16: Git Bisect - Finding Bugs in History

## 🎯 Objective
Master git bisect to efficiently find when a bug was introduced using binary search through commit history.

## 📚 Concepts Covered
- Basic bisect workflow
- Manual bisect
- Automated bisect with scripts
- Bisect on file paths
- Understanding binary search
- Bisect visualization
- Skip commits during bisect

## 📝 Preparation

```powershell
mkdir bisect-debugging
cd bisect-debugging
git init

# Create a file with a "bug" that will be introduced later
@"
function calculateTotal(items) {
  let total = 0;
  for (let item of items) {
    total += item.price;
  }
  return total;
}
module.exports = { calculateTotal };
"@ | Out-File calculator.js -Encoding UTF8

git add calculator.js
git commit -m "Initial calculator implementation"
```

Now let's create a history where a bug is introduced:

```powershell
# Commit 2: Add discount feature (works fine)
@"
function calculateTotal(items) {
  let total = 0;
  for (let item of items) {
    total += item.price;
  }
  return total;
}

function applyDiscount(total, discount) {
  return total - discount;
}

module.exports = { calculateTotal, applyDiscount };
"@ | Out-File calculator.js -Encoding UTF8
git commit -am "Add discount feature"

# Commit 3: Add tax calculation (works fine)
@"
function calculateTotal(items) {
  let total = 0;
  for (let item of items) {
    total += item.price;
  }
  return total;
}

function applyDiscount(total, discount) {
  return total - discount;
}

function calculateTax(amount, rate) {
  return amount * rate;
}

module.exports = { calculateTotal, applyDiscount, calculateTax };
"@ | Out-File calculator.js -Encoding UTF8
git commit -am "Add tax calculation"

# Commit 4: BUG INTRODUCED - Wrong calculation
@"
function calculateTotal(items) {
  let total = 0;
  for (let item of items) {
    total += item.price * 0; // BUG: Multiplying by 0!
  }
  return total;
}

function applyDiscount(total, discount) {
  return total - discount;
}

function calculateTax(amount, rate) {
  return amount * rate;
}

module.exports = { calculateTotal, applyDiscount, calculateTax };
"@ | Out-File calculator.js -Encoding UTF8
git commit -am "Optimize calculation loop"

# Commit 5: Add another feature (bug still present)
@"
function calculateTotal(items) {
  let total = 0;
  for (let item of items) {
    total += item.price * 0; // BUG still here
  }
  return total;
}

function applyDiscount(total, discount) {
  return total - discount;
}

function calculateTax(amount, rate) {
  return amount * rate;
}

function formatCurrency(amount) {
  return '$' + amount.toFixed(2);
}

module.exports = { calculateTotal, applyDiscount, calculateTax, formatCurrency };
"@ | Out-File calculator.js -Encoding UTF8
git commit -am "Add currency formatting"

# Commit 6: Add more features (bug still present)
@"
function calculateTotal(items) {
  let total = 0;
  for (let item of items) {
    total += item.price * 0; // BUG still here
  }
  return total;
}

function applyDiscount(total, discount) {
  return total - discount;
}

function calculateTax(amount, rate) {
  return amount * rate;
}

function formatCurrency(amount) {
  return '$' + amount.toFixed(2);
}

function validateItems(items) {
  return Array.isArray(items) && items.length > 0;
}

module.exports = { calculateTotal, applyDiscount, calculateTax, formatCurrency, validateItems };
"@ | Out-File calculator.js -Encoding UTF8
git commit -am "Add input validation"

# Commit 7: Add documentation
"# Calculator Module`n`nA comprehensive calculator for e-commerce." | Out-File README.md -Encoding UTF8
git add README.md
git commit -m "Add documentation"
```

## 📝 Tasks

### Task 1: Identify the Problem
1. View current calculator.js
2. Notice the bug: `total += item.price * 0`
3. We know the bug exists now, but it worked before
4. Use bisect to find WHEN it was introduced

### Task 2: Start Bisect
1. Start bisect: `git bisect start`
2. Mark current commit as bad: `git bisect bad`
3. View the first commit: `git log --oneline | Select-Object -Last 1`
4. Mark first commit as good: `git bisect good <first-commit-hash>`
5. Git checks out middle commit

### Task 3: Test Middle Commit
1. Git checked out a middle commit
2. View calculator.js
3. Look for the bug (price * 0)
4. If bug exists: `git bisect bad`
5. If bug doesn't exist: `git bisect good`
6. Git will checkout another commit

### Task 4: Continue Bisecting
1. Keep testing each commit Git checks out
2. Mark as good or bad
3. View the bisect log as you go: `git bisect log`
4. Git will narrow down to the exact commit
5. Eventually Git will say: "abc123 is the first bad commit"

### Task 5: Understand Binary Search
Understand what Git did:
```
Commits: C1(G) - C2 - C3 - C4 - C5 - C6 - C7(B)

Step 1: Test C4 (middle)
C4 is bad → bug in C1-C4

Step 2: Test C2 (middle of C1-C4)
C2 is good → bug in C3-C4

Step 3: Test C3
C3 is good → bug is in C4!

Result: C4 introduced the bug
Only 3 tests for 7 commits!
```

### Task 6: View Bisect Results
1. After bisect finishes, view the bad commit: `git show <bad-commit-hash>`
2. See exactly what changed in that commit
3. Find the bug: "Optimize calculation loop" introduced `* 0`
4. Exit bisect: `git bisect reset`
5. Returns to HEAD

### Task 7: Automated Bisect with Script
1. Create test script `test.js`:
   ```javascript
   const { calculateTotal } = require('./calculator.js');
   const items = [
     { price: 10 },
     { price: 20 },
     { price: 30 }
   ];
   const total = calculateTotal(items);
   if (total === 60) {
     process.exit(0); // Good
   } else {
     process.exit(1); // Bad
   }
   ```
2. Start bisect: `git bisect start HEAD <first-commit-hash>`
3. Run automated bisect: `git bisect run node test.js`
4. Git automatically tests each commit!
5. Finds bad commit without manual testing

### Task 8: Bisect with Skip
1. Start new bisect
2. If you encounter a commit that can't be tested (build broken, etc.)
3. Skip it: `git bisect skip`
4. Git will try adjacent commits
5. Continue bisecting

### Task 9: Bisect Visualization
1. During bisect, visualize progress:
   ```powershell
   git bisect visualize --oneline
   ```
2. Or create graph:
   ```powershell
   git log --oneline --graph --all --decorate
   ```
3. See which commits are being tested

### Task 10: Bisect on Specific File
1. Start bisect
2. Only care about changes to specific file
3. Use: `git bisect start -- calculator.js`
4. Bisect only considers commits that touched that file
5. Faster for targeted debugging

### Task 11: Bisect Terms (Custom Good/Bad)
1. Start bisect with custom terms:
   ```powershell
   git bisect start --term-old=works --term-new=broken
   git bisect broken
   git bisect works <hash>
   ```
2. More intuitive language
3. Use any terms you want

### Task 12: Real-World Scenario - Performance Regression
Create a new scenario:
1. Create commits that gradually slow down code
2. Create performance test script
3. Use bisect to find when performance degraded
4. Script checks execution time
5. Automated bisect finds the commit

### Task 13: Bisect with Multiple Bad Commits
1. Sometimes bugs are introduced in multiple steps
2. Find first bad, fix it
3. Run bisect again to find if another exists
4. Repeat until all found

### Task 14: Bisect Log and Replay
1. During bisect, view log: `git bisect log`
2. Save log: `git bisect log > bisect-session.txt`
3. Later replay: `git bisect replay bisect-session.txt`
4. Useful for sharing debugging session

### Task 15: Complex Bisect with Build Steps
1. Create scenario where each commit needs building
2. Create script that:
   - Builds project: `npm install`
   - Runs tests: `npm test`
   - Returns 0 if pass, 1 if fail
3. Use: `git bisect run ./build-and-test.sh`
4. Automated bisect with complex verification

## ✅ Expected Outcome

You should know:
- How to use git bisect to find bugs
- Binary search concept in Git
- Automated bisect with scripts
- How to skip problematic commits
- Real-world debugging workflows

## 🎓 Key Concepts

### Binary Search Efficiency
```
Linear search: Test each commit (n tests)
Binary search: Test log₂(n) commits

For 100 commits:
Linear: 100 tests
Binary: 7 tests!
```

### Bisect Workflow
```
1. git bisect start
2. git bisect bad [commit]    # Mark bad
3. git bisect good [commit]   # Mark good
4. Test current checkout
5. Mark as good or bad
6. Repeat until found
7. git bisect reset
```

## 🔍 Verification Commands

```powershell
# Check bisect status
git bisect log

# Visualize bisect
git bisect visualize

# See what's being tested
git status

# After finding, view the commit
git show <bad-commit-hash>
```

## 💡 Pro Tips

### Creating Test Scripts
```javascript
// test.js - Exit 0 for good, 1 for bad
const result = testSomething();
process.exit(result ? 0 : 1);
```

```powershell
# test.ps1 - PowerShell version
$result = Test-Something
if ($result) { exit 0 } else { exit 1 }
```

### Quick Bisect
```powershell
# One-liner bisect
git bisect start HEAD v1.0 -- ; git bisect run npm test
```

### Bisect Best Practices
1. **Ensure tests are reliable**: Flaky tests ruin bisect
2. **Use automated bisect**: Faster and more reliable
3. **Keep commits atomic**: Easier to identify culprit
4. **Test with same environment**: Consistent results

## 🎯 What You Should Know Now

- ✅ How git bisect works
- ✅ Binary search concept
- ✅ Manual bisect workflow
- ✅ Automated bisect with scripts
- ✅ When to use bisect
- ✅ How to interpret results
- ✅ Real-world debugging scenarios

## 📊 Bisect Commands Cheatsheet

```powershell
# Start bisect
git bisect start
git bisect bad [commit]
git bisect good [commit]

# Mark commits
git bisect good              # Current is good
git bisect bad               # Current is bad
git bisect skip              # Can't test current

# Automated
git bisect run <test-command>

# Information
git bisect log               # Show bisect history
git bisect visualize         # Show graph

# Control
git bisect reset             # End bisect
git bisect replay <file>     # Replay session

# Advanced
git bisect start -- <paths>  # Bisect specific files
git bisect start --term-old=works --term-new=broken
```

## 💼 Real-World Use Cases

### Use Case 1: Find Performance Regression
```powershell
# Create benchmark script
"node benchmark.js" | Out-File test.sh
git bisect start HEAD v1.0
git bisect run powershell ./test.sh
```

### Use Case 2: Find When Test Started Failing
```powershell
git bisect start
git bisect bad HEAD
git bisect good v2.0.0
git bisect run npm test
```

### Use Case 3: Find UI Bug Introduction
```powershell
# Script takes screenshot and compares
git bisect start HEAD last-known-good
git bisect run ./visual-test.sh
```

## 🐛 Troubleshooting

### If Build Breaks
```powershell
git bisect skip  # Skip this commit
# Git tries adjacent commits
```

### If Test is Flaky
```powershell
# Run test multiple times in script
for i in 1..5 {
  npm test
  if ($LASTEXITCODE -ne 0) { exit 1 }
}
exit 0
```

### If Bisect Gets Confused
```powershell
git bisect reset  # Start over
# Be more careful with good/bad marks
```

## ⏭️ Next Exercise
Ready for **Exercise 17: Reflog and Recovery** - recovering lost commits!

---

**Time to Complete**: 35 minutes  
**Difficulty**: ⭐⭐⭐ Advanced  
**Prerequisites**: Exercise 01-15
