# Solution: Exercise 16 - Git Bisect

## Complete Command Sequence

```powershell
# Preparation - Create history with bug
mkdir bisect-practice
cd bisect-practice
git init

# Commit 1 (good)
"function calc(x) { return x * 2; }" | Out-File calc.js -Encoding UTF8
git add calc.js
git commit -m "Add calc function"

# Commit 2 (good)
"function add(a, b) { return a + b; }" | Add-Content calc.js
git commit -am "Add add function"

# Commit 3 (good)
"function subtract(a, b) { return a - b; }" | Add-Content calc.js
git commit -am "Add subtract function"

# Commit 4 (BAD - introduce bug)
"function multiply(a, b) { return a * c; }" | Add-Content calc.js
git commit -am "Add multiply function"

# Commit 5 (still bad)
"function divide(a, b) { return a / b; }" | Add-Content calc.js
git commit -am "Add divide function"

# Commit 6 (still bad)
"// Math utilities" | Add-Content calc.js
git commit -am "Add comment"

# Task 1: Start bisect
git bisect start

# Task 2: Mark current as bad
git bisect bad

# Task 3: Mark old commit as good
git log --oneline
git bisect good <first-commit-hash>
# Git checks out middle commit

# Task 4: Test and mark
# Check calc.js - is multiply function correct?
Get-Content calc.js
# If bug present: git bisect bad
# If no bug: git bisect good

# Task 5: Continue until found
git bisect bad  # or good based on testing
# Git checks out another commit
# Repeat testing until Git identifies the bad commit

# Task 6: End bisect
git bisect reset
# Returns to original HEAD

# Task 7: Bisect with script
# Create test script
@"
Select-String -Pattern 'return a \* c' calc.js
if (`$?) { exit 1 } else { exit 0 }
"@ | Out-File test.ps1 -Encoding UTF8

git bisect start
git bisect bad HEAD
git bisect good <first-commit>
git bisect run powershell -File test.ps1
# Automatically finds bad commit!

# Task 8: Bisect log
git bisect start
git bisect bad
git bisect good <first-commit>
git bisect bad  # Test some commits
git bisect good
git bisect log > bisect-log.txt
Get-Content bisect-log.txt

# Task 9: Bisect replay
git bisect reset
git bisect replay bisect-log.txt
# Replays the bisect session

# Task 10: Skip commits
git bisect start
git bisect bad HEAD
git bisect good <first-commit>
# Can't test current commit
git bisect skip
# Tests next commit instead

# Task 11: Visualize bisect
git bisect start
git bisect bad
git bisect good <first-commit>
git bisect visualize
# Shows commits being bisected

# Task 12: Bisect with terms
git bisect start --term-old=working --term-new=broken
git bisect broken
git bisect working <commit>
# Use custom terms instead of good/bad

# Task 13: Find performance regression
# Create commits with performance change
"for i in 1..100" | Out-File perf.ps1 -Encoding UTF8
git add perf.ps1
git commit -m "Performance test"
"for i in 1..100000" | Out-File perf.ps1 -Encoding UTF8
git commit -am "Slow performance"

@"
`$time = Measure-Command { & ./perf.ps1 }
if (`$time.TotalSeconds -gt 1) { exit 1 } else { exit 0 }
"@ | Out-File perftest.ps1 -Encoding UTF8

git bisect start
git bisect bad HEAD
git bisect good HEAD~5
git bisect run powershell -File perftest.ps1

# Task 14: Bisect with multiple good commits
git bisect start
git bisect bad HEAD
git bisect good <commit1>
git bisect good <commit2>
git bisect good <commit3>
# Can mark multiple known-good commits

# Task 15: Complete workflow
# Bug reported: "multiply function broken"
git bisect start
git bisect bad HEAD
# Find last known-good version
git bisect good v1.0.0

# Test each commit manually
# Check calc.js multiply function
git bisect bad  # Bug present
# Git checks out another
git bisect good  # Bug not present
# Git checks out another
git bisect bad  # Bug present
# Git identifies: "abc1234 is first bad commit"

# View the bad commit
git show abc1234
# See the bug: "return a * c" should be "return a * b"

git bisect reset
# Fix the bug
"function multiply(a, b) { return a * b; }" | Out-File calc.js -Encoding UTF8
git commit -am "fix: correct multiply function variable"
```

## Bisect Process

### Manual Bisect
```
1. git bisect start
2. git bisect bad            # Current is bad
3. git bisect good <commit>  # Old commit is good
4. Test current commit
5. git bisect bad/good       # Mark result
6. Repeat steps 4-5
7. Git finds first bad commit
8. git bisect reset
```

### Automated Bisect
```
1. Write test script (exit 0 = good, exit 1 = bad)
2. git bisect start
3. git bisect bad
4. git bisect good <commit>
5. git bisect run <test-script>
6. Git automatically finds bad commit
7. git bisect reset
```

## Important Commands

```powershell
# Start/End
git bisect start
git bisect reset

# Mark commits
git bisect bad [<commit>]
git bisect good <commit>
git bisect skip

# Automated
git bisect run <command>

# Visualization
git bisect log
git bisect visualize
git bisect view

# Replay
git bisect log > file
git bisect replay file

# Custom terms
git bisect start --term-old=working --term-new=broken
git bisect broken
git bisect working <commit>
```

## Test Script Requirements

### Exit Codes
- `0` = Good/working commit
- `1-127` (except 125) = Bad/broken commit
- `125` = Cannot test (skip)

### PowerShell Example
```powershell
# test.ps1
$result = node test.js
if ($LASTEXITCODE -eq 0) { exit 0 } else { exit 1 }
```

### Bash Example
```bash
#!/bin/bash
make && make test
```

## Common Use Cases

### Find Bug Introduction
```powershell
git bisect start
git bisect bad
git bisect good v1.0.0
# Test each commit
git bisect reset
```

### Find Performance Regression
```powershell
git bisect start
git bisect bad
git bisect good <last-fast-commit>
git bisect run ./performance-test.sh
```

### Find Breaking Change
```powershell
git bisect start
git bisect bad
git bisect good <last-working>
git bisect run npm test
```

## Verification

✅ You should now understand:
- Binary search debugging
- Manual bisecting
- Automated bisecting
- Writing test scripts
- Finding bug introductions
- Performance regression hunting

## Pro Tips

### Test Script Template
```powershell
# test-bisect.ps1
# Build project
npm install
npm run build

# Run tests
npm test

# Exit with appropriate code
if ($LASTEXITCODE -eq 0) {
    exit 0  # Good
} else {
    exit 1  # Bad
}
```

### Skip Untestable Commits
```powershell
# If build fails
git bisect skip
```

### View Bisect Progress
```powershell
git bisect log
# Shows all your good/bad marks
```

### Bisect Between Tags
```powershell
git bisect start
git bisect bad v2.0.0
git bisect good v1.5.0
```

### Save Bisect Session
```powershell
git bisect log > bisect-session.txt
# Resume later:
git bisect replay bisect-session.txt
```

---

**Exercise completed!** ✅  
You now master binary search debugging with bisect!
