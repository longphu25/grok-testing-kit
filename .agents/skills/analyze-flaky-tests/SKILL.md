---
name: analyze-flaky-tests
description: Analyze unstable automation tests (flaky), identify root causes and automatically fix them. Supports 2 modes — ANALYZE (report only) and FIX (report + self-correct code).
---

# Workflow: Analyze & Fix Flaky Tests

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/flaky-test-analyzer`** (in`.agents/skills/flaky-test-analyzer/SKILL.md`) before starting. Additionally, refer to additional skills **`/smart-locator-agent`** and **`/locator-healer-agent`** when needing to repair/replace locator.

This workflow helps the agent automatically analyze unstable automation tests (sometimes pass and sometimes fail), determine the exact root cause, and (depending on mode) automatically fix the code to stabilize the test.

## ⚠️ Implementation principles

- **All output in English**
- **DO NOT guess** the cause — must analyze the log, code, and actual DOM
- **Must wait for user confirmation** fix list in Step 3 before editing the code (Mode FIX)
- If the user has not provided a test file/error log → ask before starting
- ⚠️ After the user confirms the fix → the agent self-corrects + verifies, DOES NOT ask again during the fixing process

## 2 Modes

| Mode | When to use | Output |
|---|---|---|
| **ANALYZE** (default) | Users need to understand the cause of flaky, do not want to edit the code | Analysis report + Fix suggestions |
| **FIX** | The user wants the agent to fix the code itself | Like ANALYZE + Corrected code + Verification results |

> If the user says "fix it now", "fix it for me", "fix this test", or asks the agent to fix the code → automatically switches to **Mode FIX**.

## Input to collect

The agent needs at least **1 of the following inputs** from the user:

| Input | How to get | Priority |
|---|---|---|
| **Test file path** | User provided or agent found in project | ⭐ Required |
| **Error log / stack trace** | User paste or agent runs test to collect | ⭐ Required |
| **CI/CD log** | User provides URL or log file | Options |
| **Test report** (HTML/JSON) | Playwright report, Allure, TestNG report | Options |
| **Fail history** | Number of failures / total number of runs | Options |

## Steps to perform

### Step 1: Collect information & Reproduce errors (Detect & Reproduce)

1. **Read test file** with`view_file`:
   - Identify framework (Playwright / Selenium / Appium / Pytest / TestNG)
   - Record structure: Page Objects, fixtures, helper functions
   - Mark suspicious code areas (waits, locators, assertions, setup/teardown)

2. **Run test** to reproduce the error (if there is no error log):
   - Run the test **3 times in a row** with`run_command`:
     ```bash
     # Playwright
     npx playwright test <test_file> --retries=0 --reporter=list

     # Pytest
     python -m pytest <test_file> -v --count=3

     # Maven/TestNG
     mvn test -Dtest=<TestClass> -Dsurefire.rerunFailingTestsCount=0
     ```- Record pattern: Fail every time? Fail at which step? Error messages are the same or different?

3. **Collect evidence:**
   - Error log / stack trace from each run
   - Screenshots (if test has capture on failure)
   - Console log / Network log (if related to API calls)

### Step 2: Root Cause Analysis (Inspect & Classify)

1. **Read error log** and classify according to root cause table:

| Category | Identification signs | For example Error |
|---|---|---|
| 🎯 **Locator is unstable** |`ElementNotFound`, `No matching element`, the selector contains dynamic class/index |`Locator('.css-1a2b3c')`fail because class changes every build |
| ⏱️ **Timing / Race condition** |`Timeout`, `Element not visible`, test pass when running slowly (debug mode) |`expect(element).toBeVisible()`timeout because the animation has not finished |
| 📊 **Test data conflicts** |`Duplicate key`, `Already exists`, fails when running parallel | 2 tests create the same user`test@email.com`|
| 🔄 **State dependency** | Pass when run independently, fail when run with suite | Test B depends on the data that Test A generates |
| 🌐 **Environment / Network** |`ECONNREFUSED`, `503`, `CORS error`, fails on CI but passes local | Slow API server, CDN timeout |
| 🖼️ **UI Animation / Transition** |`Element is not clickable`, `intercept`, fail randomly when clicking | Modal animation not completed, overlay covers button |
| 📱 **Viewport / Responsive** | Element is hidden, doesn't scroll, fails at other resolutions | Button is outside viewport on CI (headless 800x600) |
| 🧹 **Cleanup / Teardown** | Fail on 2nd run+, pass first time | Test does not cleanup data → next time there will be conflict |

2. **Inspect code** in detail, check each anti-pattern:

   **Locator check:**
   - [ ] Can use dynamic class (`css-xxx`, `sc-xxx`, `MuiXxx-root`)? → ❌
   - [ ] Can use positional xpath (`//div[3]/button[2]`)? → ❌
   - [ ] Is Locator unique? (check on actual DOM if needed)

   **Wait strategy check:**
   - [ ] Yes`waitForTimeout()` / `Thread.sleep()` / `time.sleep()`? → ❌
   - [ ] Yes`waitForSelector()` khi `expect()`enough? → ⚠️
   - [ ] Does Assertion have an appropriate timeout?

   **Test data check:**
   - [ ] Is the data hardcoded? (`test@email.com`, `user123`)
   - [ ] Is the data unique per run? (timestamp / random)
   - [ ] Is there data cleanup after testing?

   **Test independence check:**
   - [ ] Does the test depend on running order?
   - [ ] Does the test share state via global variables?
   - [ ] Is Setup/Teardown complete?

3. **If you need to inspect the actual DOM** (locator issue):
   - Open browser with MCP:`browser_navigate` → `browser_resize(1920, 1080)` → `browser_snapshot`- Compare locator in code vs actual DOM
   - Identify a more stable alternative locator (using skill`/smart-locator-agent`)

### Step 3: Make a report & Propose fixes (Report — CHECKPOINT)

1. **Create artifact**`flaky_analysis.md`with structure:```markdown
# Flaky Test Analysis Report

## Overview
   - **Test file:** <path>
   - **Framework:** Playwright / Selenium / ...
- **Failure frequency:** X/Y runs
   - **Severity:** 🔴 Critical / 🟡 Medium / 🟢 Low

   ## Root Cause Analysis

| # | Position (Line) | Category | Problem Description | Level |
   |---|---|---|---|---|
| 1 | test.ts:45 | ⏱️ Timing | waitForTimeout(3000) instead of smart wait | 🔴 |
   | 2 | page.ts:12 | 🎯 Locator | .css-1abc dynamic class | 🟡 |

## Recommended Fix

| # | Problem | Current code | Recommended code | Reason |
   |---|---|---|---|---|
| 1 | Hard sleep | `waitForTimeout(3000)` | `expect(el).toBeVisible()` | Smart wait self-retry |
   | 2 | Dynamic class | `.css-1abc` | `getByRole('button', {name: 'Submit'})` | Semantic, no CSS dependencies |
   ```2. **⏸️ STOP — Present for user review:**
   - List of identified root causes
   - Fix proposal table (old code → new code)
   - Ask: "Do you agree with this analysis? Should I proceed to fix the code or just report?"

3. If the user selects **Mode ANALYZE** → **END** here
4. If the user agrees to fix → move to Step 4

### Step 4: Automatically fix the code (Auto-Fix — Mode FIX)

> Only do this when in **Mode FIX** and the user has confirmed

1. **Fix the code** in order of priority (fix the most serious first):

   **Fix Locator:**
   - Use skills`/locator-healer-agent`to replace broken locator
   - Comply with internal locator priority`.agents/rules/locator_strategy.md`- Verify the new locator on the actual DOM before committing to the code

   **Fix Timing:**```
   ❌ page.waitForTimeout(3000)
   ✅ await expect(page.getByRole('button')).toBeVisible()

   ❌ Thread.sleep(5000)
   ✅ wait.until(ExpectedConditions.visibilityOfElementLocated(...))
   ```

   **Fix Test Data:**
   ```
   ❌ const email = "test@email.com"
   ✅ const email = `auto_${Date.now()}@test.com`
   ```**Fix Test Independence:**
   - Add setup/teardown to create and cleanup private data
   - Eliminate dependencies between test cases
   - Make sure each test creates its own preconditions

2. **Usage**`replace_file_content`or`multi_replace_file_content`to apply changes
3. **Note** each change to the artifact`flaky_analysis.md`### Step 5: Verify & Ensure Stability (Verify Stability)

> Only execute when in **Mode FIX**

1. **Run the test 3 times in a row** after fixing:```bash
   # Playwright
   npx playwright test <test_file> --retries=0 --repeat-each=3

   # Pytest
   python -m pytest <test_file> -v --count=3

   # Maven
   mvn test -Dtest=<TestClass> (chạy 3 lần thủ công)
   ```2. **Evaluate results:**

   | Results | Action |
   |---|---|
   | ✅ **3/3 PASS** | Test is stable → artifact update, success report |
   | ⚠️ **2/3 PASS** | Still flaky → go back to Step 2 analyze failed attempts, fix again (maximum 3 rounds) |
   | ❌ **0-1/3 PASS** | Fix wrong direction → rollback, re-analyze root cause |

3. **Stability Checklist** (must be completed in full before ending):
   - [ ] Locator unique and stable over many reloads
   - [ ] No hard sleep / fixed delay
   - [ ] Test unique + traceable data each run
   - [ ] Independent test — regardless of running order
   - [ ] Test pass **3+ times in a row**
   - [ ] No more debug log / commented code

4. **Update artifact**`flaky_analysis.md`:
   - Added section "Results after fixing" with the running results table
   - Mark status: ✅ STABILIZED / ⚠️ PARTIALLY FIXED / ❌ STILL FLAKY

## Handle special situations

| Situation | How to handle |
|---|---|
| **Test only fails on CI** | Compare CI vs local env (viewport, timezone, headless, resources). Check the viewport`1920x1080`Can it be set on CI? |
| **Test fails when running parallel** | Check test data isolation, shared state, database locks. Recommended unique data per worker |
| **Test fails after new deployment** | Check DOM changes, API response changes. Use`/locator-healer-agent`to update locators |
| **Test fails randomly without pattern** | Collect more data (run 10+ times), check memory leak, resource exhaustion |
| **Multiple tests with flaky** | Find common factor (shared fixture, shared page object, shared config) |
| **Test failed due to external API** | Propose mock/stub external dependencies, retry logic for API calls |

## Output

### Mode ANALYZE
- Artifact`flaky_analysis.md`:
  - Test overview (files, framework, failure frequency)
  - Root Cause Analysis (classification table)
  - Propose detailed fixes (old code → new code)
  - Stability checklist

### Mode FIX
- All Mode ANALYZE output, plus:
  - Code has been fixed (files have changed)
  - Verification results (3 PASS/FAIL runs)
  - Final status: STABILIZED / PARTIALLY FIXED / STILL FLAKY