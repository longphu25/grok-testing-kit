---
name: generate-automation-from-testcases
description: Convert manual test cases into automation scripts autonomously using the 6-step AI-RBT Framework and Codex tools.
---

# Workflow: Generate Automation Scripts from Manual Test Cases

> **MANDATORY SKILLS:** You MUST load and carefully read the content of the following skills before starting:
> - **`/qa-automation-engineer`** (`.agents/skills/qa-automation-engineer/SKILL.md`) — General automation rules + workflow routing
> - **`/ui-debug-agent`** (`.agents/skills/ui-debug-agent/SKILL.md`) — Inspect DOM, collect locators
> - **`/smart-locator-agent`** (`.agents/skills/smart-locator-agent/SKILL.md`) — Generate stable locator
> - **`/test-data-generator`** (`.agents/skills/test-data-generator/SKILL.md`) — Generate unique, traceable test data

This workflow helps the agent read the manual test cases file provided by the user, open the browser to inspect the UI, collect actual locators, generate complete automation scripts (POM + Test), run tests and self-correct errors until PASS.

## ⚠️ Implementation principles

- **Role:** Agent plays the role of Senior Automation Engineer — clean Code + POM compliant
- **All output in English**
- **ABSOLUTELY DO NOT GUESS the locator** — must inspect the actual DOM using MCP browser tools
- **Desktop viewport 1920×1080** for all UI debugging
- ⚠️ **Rule E3 (CRITICAL):** When testing FAIL → read the log yourself → analyze → fix the code → run again. **BONUS asking users during the error fixing process.** Only ask when encountering conflicting business rules or after 5 auto-heal rounds have passed.
- **Artifact`task.md`** — MUST create to track 6-step progress

## How is this Workflow different?`/generate-automation-from-ui-flow`?

| | `from_testcases`(this workflow) |`from_ui_flow`|
|---|---|---|
| **Input** | Structured test case manual file (MD/Excel/JSON) | Describe UI steps in words or just URL |
| **TC available** | ✅ Available — read and convert | ❌ Not yet — self-discovery agent |
| **Approach** | Read TC → inspect UI verify → generate code | Run it in the browser → collect → generate code |

## Input to collect

| Input | How to get | Priority |
|---|---|---|
| **File test cases** (MD/Excel/JSON/URL) | User provides path or URL | ⭐ Required |
| **Application URL** | User provided or in TC | ⭐ Required |
| **Credentials** (if login required) | User provides or uses available fixture | Options |
| **Tech stack** | User specifies or detects from project | Options |

If the user has not provided enough → ask before starting.

## Steps to perform

### Step 1: Initialize, Analyze & Plan (Context & Analysis)

1. **Read the test case file** provided by the user:
   - File local →`view_file`
   - URL (Google Sheets, Confluence, etc.) → `read_url_content`- Determine format: Markdown table, Excel, JSON, or free-form text

2. **Parse test cases** and extract:
   - TC list (ID, Title, Steps, Expected Results, Test Data, Priority)
   - The pages/screens that TC goes through
   - Pre-conditions (login, setup data, navigate...)
   - Dependencies between TCs (if any)

3. **Determine tech stack** (if not clear):

   | Frameworks | Language | Runner | When to choose |
   |---|---|---|---|
   | Playwright | TypeScript | Playwright Test | Default for web |
   | Playwright | Python | Pytest | When the project uses Python |
   | Selenium | Java | TestNG | When the user requests Java |
   | Appium | Java | TestNG | Mobile app |

4. **Create artifact`task.md`** to track progress:```markdown
   # Automation Generation Progress
- [x] Step 1: Analyze test cases
   - [ ] Step 2: UI Survey (MCP Recon)
   - [ ] Step 3: Design POM
   - [ ] Step 4: Prepare test data
   - [ ] Step 5: Generate automation scripts
   - [ ] Step 6: Run test + Auto-heal

   ## Test Cases to Automate
   | TC ID | Title | Pages | Priority | Status |
   |---|---|---|---|---|
| TC01 | Login successful | LoginPage, DashboardPage | P1 | ⏳ |
   | TC02 | Login sai password | LoginPage | P1 | ⏳ |
   ```### Step 2: Automated UI survey using MCP (Autonomous UI Recon)

1. **Open browser** with MCP and navigate according to test case steps:```
browser_navigate → Application URL
   browser_resize → 1920 × 1080
browser_wait_for → page load completed
   browser_snapshot → DOM capture
   ```2. **For each page in the test case**, do:
   -`browser_snapshot`→ read accessibility tree
   - Identify all elements that need interaction (inputs, buttons, links, dropdowns...)
   - Collect the best locator for each element (according to priority in skill`/smart-locator-agent`)
   - Verify locator by interactive testing (`browser_click`, `browser_type`)

3. **Record in Locator Collection table:**

   | Page | Element | Action | Primary Locator | Fallback Locator | Verified |
   |---|---|---|---|---|---|
   | LoginPage | Email input | Type |`getByLabel('Email')` | `#email` | ✅ |
   | LoginPage | Password input | Type | `getByLabel('Password')` | `#password` | ✅ |
   | LoginPage | Login button | Click | `getByRole('button', {name: 'Login'})` | `button[type=submit]` | ✅ |
   | DashboardPage | Welcome text | Assert | `getByRole('heading', {name: /Welcome/})` | `.welcome-header`| ✅ |

4. **Handling the situation:**

   | Situation | How to handle |
   |---|---|
   | URL blocked / need VPN | User notification |
   | Login required | Use existing fixture or ask user credentials |
   | Element not found | Snapshot again → try another locator → notify user if DOM changes |
   | CAPTCHA / 2FA | User notification — cannot automate |
   | Dynamic content / SPA |`browser_wait_for`specific text before snapshot |

5. **ABSOLUTELY DO NOT RULE selectors** — every locator must be verified on the actual DOM.

### Step 3: Design POM (Page Object Model Architecture)

1. **Determine the list of Page classes** to create:
   - Each page/screen in test flow → 1 Page class
   - Consider creating`BasePage`if not already in the project

2. **Generate Page Object classes** with`write_to_file`:

   **Structure of each Page class:**```
- Locators (declared at the top of the class — from Step 2)
   - Constructor (receive page/driver instance)
   - Action methods (describe user behavior, not DOM)
   - Verification methods (check state/text after action)
   ```**Principles:**
   - Method name describes the behavior:`login()`, `fillRegistrationForm()`, Not`clickButton()`- No hardcode waits — only smart waits
   - Locator taken from Step 2 (verified) — NO GUESSING
   - Return`this`or next page object for method chaining (if appropriate)

3. **Check current project structure:**
   - If the project already has pages/ → generate the file in the correct directory
   - If new project → create structure according to skill`/framework-architect`- Do not create duplicates — check if the page already exists before creating a new one

### Step 4: Prepare Data (Test Data Strategy)

1. **Analyze test data** from test cases:
   - What data needs to be **unique per run** (email, username, ID) → generate random + traceable
   - Which data is **fixed** (URL, config values) → read from env/config
   - Which data needs **multiple sets** (data-driven) → create external file (JSON/YAML)

2. **Generate test data utilities** (using skill`/test-data-generator`):
   ```
   Format: <prefix>_<testName>_<timestamp>
For example:
   - Email:    auto_login_1712049200@test.com
   - Username: auto_user_1712049200
   - Code:     TC_REG_1712049200
   ```3. **Sensitive data** (credentials):
   - Read from env variables or config file
   - **NO hardcode** in test code
   - **DO NOT read .env directly** (security rule)

### Step 5: Generate Automation Scripts (Test Classes)

1. **Create test classes** — each test case or group of related tests → 1 test file:

   **Structure of each test:**```
   Setup (Arrange):
- Initialize page objects
   - Prepare test data
   - Navigate to the page to test
   - Login (if necessary, via fixture)

   Execution (Act):
- Follow the steps according to the test case
   - Call methods from Page Objects

   Verification (Assert):
- Assert results with expected results from TC
   - Assertion message is clear, easy to debug when it fails
   ```2. **Required Assertions:**
   - Each TC MUST have at least 1 assertion
   - Assert message clearly describes:`"Expected dashboard to show after login"`- Use soft assertions when you need to check many points
   - Appropriate timeout (don't leave the default too short)

3. **Coding principles:**
   - No`waitForTimeout()` / `Thread.sleep()`— only smart waits
   - Do not inline locator in test — locator in Page class
   - Import neatly — no unused imports
   - Test independent — does not depend on running order
   - Cleanup/teardown if test creates data

### Step 6: Execution & Auto-Heal — RULE E3

1. **Run test** with`run_command`:
   ```bash
   # Playwright TS
   npx playwright test <test_file> --headed

   # Playwright Python
   python -m pytest <test_file> --headed

   # Selenium Java
   mvn test -Dtest=<TestClass>
   ```2. **Track results** over`command_status`:

   **If PASS:**
   - Run **one more time** to confirm stability
   - Update`task.md`: TC status → ✅ PASS
   - Cleanup debug logs, commented code

   **If FAIL → Enter Auto-Heal loop:**```
WHILE test FAIL (maximum 5 rounds):
     1. Read error log / stack trace → determine step failure
     2. Error classification:

| Error | Action |
        |---|---|
| Element not found | Open MCP → snapshot → verify/replace locator |
        | Click intercepted | Wait for the overlay to disappear → retry click |
        | Timeouts | Increase timeout or add wait condition |
        | Assertion failed | Check expected value vs actual → update assertion |
        | Navigation error | Check URL, redirect, page load |
        | Test data conflicts | Generate new unique data |
        | Import/compile error | Edit import, check class name |

3. Edit the code with replace_file_content / multi_replace_file_content
     4. Rerun the test
     5. Log into task.md: "Round 2: Fix locator XYZ → PASS"
   ```3. **⚠️ Rule E3 — PROHIBITED ASKING USER when fixing errors.** Only asked when:
   - Business logic is contradictory (TC says A but the app shows B)
   - Server/app is not accessible
   - 5 auto-heal rounds have passed but still failed

4. **Verify stability** — test must PASS **twice in a row**:```bash
   # Playwright
   npx playwright test <test_file> --repeat-each=2 --retries=0
   ```### Step 7: Cleanup & Delivery

1. **Code cleanup** (required):
   - [ ] Delete`console.log()` / `print()`/temporary debug log
   - [ ] Delete unused locator
   - [ ] Delete commented-out code
   - [ ] No more`waitForTimeout()` / `Thread.sleep()`- [ ] No longer hardcoded test data (email, password)
   - [ ] Import neatly — no unused imports

2. **Update artifact`task.md`** with final result:```markdown
## Results
   | TC ID | Title | Status | Stability | Notes |
   |---|---|---|---|---|
| TC01 | Login successful | ✅ PASS | 2/2 stable | — |
   | TC02 | Login sai password | ✅ PASS | 2/2 stable | — |
| TC03 | Register an account | ⚠️ SKIP | — | Need CAPTCHA |

   ## Files Created
   - src/pages/login.page.ts
   - src/pages/dashboard.page.ts
   - src/tests/login.spec.ts
   ```3. **Report** to user:
   - Total: X TC PASS / Y TC FAIL / Z TC SKIP
   - List of created/modified files
   - Known issues / limitations
   - Locator Collection table (reference)

## Output

- **Artifact`task.md`** — progress checklist + test results
- **Page Object classes** — 1 file per page, locators verified from DOM
- **Test classes** — complete automation scripts, PASS stable
- **Test data utilities** — generators for unique + traceable data
- **Locator Collection table** — all elements + primary/fallback locators
- **Result Report** — PASS/FAIL/SKIP summary