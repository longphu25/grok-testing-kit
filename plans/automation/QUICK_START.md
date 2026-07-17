# 📋 Quick Guide: Automation Testing 6 Steps

## Two ways to use

### Method 1: One-Click (Fully automatic — Recommended)

```
/generate-automation-from-testcases

URL: [https://your-app.com/login]
Account: [admin@test.com / Test@123]
Framework: [Playwright TypeScript / Selenium Java]

Manual Test Cases:
[Paste test cases here]
```

→ Agent automatically runs all 6 steps, self-fixes until PASS test.

---

### Method 2: Sequential (Step by step)

| Step | Prompt send Antigravity | Wait User? |
|-------|------------------------|-----------|
| **0** | See`project_architecture/README.md`| Setup once |
| **1** | Copy`01.../prompt.txt`+ fill`[...]`| ✅ Waiting for confirmation |
| **2** | Copy`02.../prompt.txt`+ fill in URL & TCs | ✅ Review locators |
| **3** | Copy`03.../prompt.txt` | Review POM |
| **4** | Copy `04.../prompt.txt` | Review nhanh |
| **5** | Copy `05.../prompt.txt`| Waiting for PASS test |
| **6** | Copy`06.../prompt.txt`| Get clean code |

### Execution flow:

```
[Step 1] Set up role + tech stack
    ↓ AI confirms → OK
[Step 2] Provide URL + Test Cases
    ↓ AI automatically opens the browser and collects locators
    ↓ ⏸️ User reviews the locators table
[Step 3] AI designs POM classes
    ↓ User reviews architecture
[Step 4] AI generates Data Generator class
    ↓  User review nhanh
[Step 5] AI generates test script + runs automatically
    ↓ Automatically fix loop until PASS ✅
[Step 6] AI cleanup code
    ↓ User receives clean code → commit ✅
```

---

## Ultimate Tips

1. **Use Method 1** for 80% of cases — fastest and most effective
2. **Use Method 2** when the project is large or needs detailed control of each module
3. **Always run in the same conversation** to let AI keep context
4. **Provide a test account** if the app requires login