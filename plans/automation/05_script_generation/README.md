# Step 5: Automation code generation (Script Generation)

**Workflow:** `/generate-automation-from-testcases`(continued)
**Skill:**`/qa-automation-engineer`

---

## Purpose

Core step — AI combines POM (Step 3) and Data Strategy (Step 4) to generate a complete Test script file, run the test, and self-correct if it fails.

## How to use

1. Send files`prompt.txt`for AI.
2. AI will:
   - Generate a complete Test script file
   - **Run the test yourself** using terminal
   - If it fails → **automatically read log, automatically repair, automatically rerun**
   - Repeat until PASS is stable
3. When testing PASS → go to Step 6.

## Code Pattern: Arrange → Act → Assert

```
Arrange: Setup data, initialize pages
Act: Perform action
Assert: Check the results
```

## Self-fix Loop

AI does the loop:```
Generate code → Run tests → FAIL?
    ├── Read error log
    ├── Root cause analysis
    ├── Edit code
    └── Rerun → PASS? → DONE ✅
```

## Note

- AI will **not ask the user** during the self-fix process (unless it encounters conflicting business rules).
- Required Smart Waits — see`.agents/rules/playwright_rules.md`or`.agents/rules/selenium_rules.md`.
- Each test case must have a clear **assertion** at the end.