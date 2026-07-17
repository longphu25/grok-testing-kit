---
name: generate-automation-from-ui-flow
description: Execute UI flow directly in the browser, collect locators from the actual DOM, and generate automation scripts. Supports Playwright, Selenium, Appium.
---

# Workflow: Generate Automation from UI Flow

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/ui-debug-agent`** (in`.agents/skills/ui-debug-agent/SKILL.md`) before starting. Also refer to more skills **`/smart-locator-agent`** to generate stable and ** locators`/qa-automation-engineer`** for general automation rules.

This workflow helps the agent **execute directly** a series of UI operations on a real browser, collect locators from the actual DOM, and generate complete automation scripts — all in one automated flow, without the need for existing manual test cases.

## ⚠️ Implementation principles

- **All output in English**
- **ABSOLUTELY DO NOT GUESS locator** — must be retrieved from the actual DOM using MCP browser tools
- **Must run each UI step on a real browser** before generating code
- **Desktop viewport 1920×1080** for all UI debugging
- ⚠️ **Rule E3:** When testing FAIL → read the log yourself → analyze → fix → run again. DO NOT ask the user

## How is this Workflow different?`/generate-automation-from-testcases`?

| | `from_testcases` | `from_ui_flow`(this workflow) |
|---|---|---|
| **Input** | File manual test cases are available | Describe UI steps in words or URL + action |
| **Approach** | Read TC → inspect UI → generate code | **Runs in real browser** → collect locator → generate code |
| **When to use** | There is a test case document | No TC yet, just know "go to this page, click that" |

## Input to collect

The agent needs at least **1 of the following inputs** from the user:

| Input | Example | Priority |
|---|---|---|
| **URL + UI steps description** | "Go to https://example.com, log in, create a new user" | ⭐ Most popular |
| **URL + recording/video** | User provides operation video | Options |
| **URL + screenshots** | User provides step-by-step photos | Options |
| **URL only** | "Automate login flow for this site" | Agent self-discovery |

If the user has not provided enough → ask:
- Application URL?
- Describe the flow that needs to be automated (step by step)?
- Credentials if login required?
- Desired framework? (default: Playwright + TypeScript)

## Steps to perform

### Step 1: Reception & Preparation (Setup)

1. **Parse UI steps** from user input:
   - Convert the verbal description into a structured list of steps:```
     Step 1: Navigate to https://example.com/login
     Step 2: Enter username "admin@test.com"
     Step 3: Enter password "***"
     Step 4: Click Login button
     Step 5: Verify dashboard is displayed
     ```2. **Confirm tech stack** with user (if unclear):

   | Frameworks | Language | When to use |
   |---|---|---|
   | **Playwright** | TypeScript | Default for web automation |
   | **Playwright** | Python | When users use Python stack |
   | **Selenium** | Java | When the user requests Java/Selenium |
   | **Appium** | Java | Mobile app automation |

3. **Create artifact`task.md`** to track progress:```markdown
   # UI Flow Automation Progress
- [ ] Step 1: Preparation — parse UI steps
   - [ ] Step 2: Run UI flow on browser — collect locators
   - [ ] Step 3: Generate Page Objects + Test scripts
   - [ ] Step 4: Run test + Auto-heal
   ```### Step 2: Run UI Flow on Browser & Collect Locators (Live Recon)

> ⚡ This is the **most important** step — distinguishing this workflow from other workflows.

1. **Open browser with MCP** and navigate to URL:```
   browser_navigate → URL
   browser_resize → 1920 × 1080
browser_wait_for → page load completed
   browser_snapshot → capture the initial DOM
   ```2. **Execute each step** according to the list, for each step:```
a. browser_snapshot → reads the DOM, determines the element to interact with
   b. Determine the best locator (according to locator priority)
   c. Execute action (click / type / select / hover)
   d. browser_snapshot → confirm action results
   e. Recorded in the locator collection table
   ```3. **Locator Collection table** (recorded after each step):

   | Step | Action | Element | Primary Locator | Fallback Locator | Verified |
   |---|---|---|---|---|---|
   | 1 | Navigate | — | — | — | ✅ |
   | 2 | Type | Username input |`getByLabel('Email')` | `#email` | ✅ |
   | 3 | Type | Password input | `getByLabel('Password')` | `#password` | ✅ |
   | 4 | Click | Login button | `getByRole('button', {name: 'Login'})` | `button[type=submit]` | ✅ |
   | 5 | Assert | Dashboard title | `getByRole('heading', {name: 'Dashboard'})` | `.dashboard-title`| ✅ |

4. **Locator Priority** (compliance`.agents/rules/locator_strategy.md`):

   **Playwright:**
   `getByRole()` → `getByLabel()` → `getByPlaceholder()` → `getByText()` → `getByTestId()` → CSS → XPath

   **Selenium:**
   `id` → `data-testid` → `name` → CSS selector → XPath

   **Appium:**
   `accessibility-id` → `id` → `name` → `xpath`(related)

5. **Handling situations when running UI:**

   | Situation | How to handle |
   |---|---|
   | Element not found |`browser_snapshot`again → check the DOM → try another locator |
   | Page has not finished loading |`browser_wait_for`text/element → retry |
   | Modal/popup appears | Handle popup first → continue flow |
   | Redirect/navigation |`browser_snapshot`new page again |
   | Need to scroll |`browser_evaluate`→ scrollIntoView |
   | Login required | Ask user credentials or use existing fixture |
   | CAPTCHA / 2FA | User notification — cannot automate |

6. **Screenshot evidence** — captured at important milestones:
   - After successful login
   - After completing the main flow
   - When encountering an error/unexpected state

### Step 3: Generate Automation Scripts (Code Generation)

1. **Generate Page Object classes** from locator collection:

   **Playwright TypeScript:**```typescript
   // src/pages/login.page.ts
   import { Page, Locator } from '@playwright/test';

   export class LoginPage {
     readonly page: Page;
     readonly emailInput: Locator;
     readonly passwordInput: Locator;
     readonly loginButton: Locator;

     constructor(page: Page) {
       this.page = page;
       this.emailInput = page.getByLabel('Email');
       this.passwordInput = page.getByLabel('Password');
       this.loginButton = page.getByRole('button', { name: 'Login' });
     }

     async login(email: string, password: string) {
       await this.emailInput.fill(email);
       await this.passwordInput.fill(password);
       await this.loginButton.click();
     }
   }
   ```

   **Selenium Java:**
   ```java
   // src/main/java/.../pages/LoginPage.java
   public class LoginPage extends BasePage {
     @FindBy(id = "email")
     private WebElement emailInput;

     @FindBy(id = "password")
     private WebElement passwordInput;

     @FindBy(css = "button[type='submit']")
     private WebElement loginButton;

     public void login(String email, String password) {
       waitAndType(emailInput, email);
       waitAndType(passwordInput, password);
       waitAndClick(loginButton);
     }
   }
   ```2. **Generate Test class:**
   - Import Page Objects
   - Structure: **Arrange → Act → Assert**
   - Clear assertions with descriptive messages
   - Test data unique + traceable (using timestamp/random)

3. **Principles for generating code:**
   - Locator MUST be taken from Step 2 (verified on DOM) — NO GUESSING
   - Do not hardcode test data (credentials read from env/config)
   - Do not use`waitForTimeout()` / `Thread.sleep()`— only smart waits
   - Method names describe user behavior, not DOM operations
   - Each page → 1 file, each test → 1 file

### Step 4: Run Test & Auto-Heal (Execution & Auto-Heal)

1. **Run test** with`run_command`:
   ```bash
   # Playwright TS
   npx playwright test <test_file> --headed

   # Playwright Python
   python -m pytest <test_file> --headed

   # Selenium Java
   mvn test -Dtest=<TestClass>
   ```2. **Track results** over`command_status`:
   - If **PASS** → switch to verify stability
   - If **FAIL** → enter Auto-Heal loop:```
WHILE test FAIL (maximum 5 rounds):
     1. Read error log → determine step failure
     2. Error classification:
        - Wrong locator → open MCP browser, inspect DOM again, change locator
        - Timing issue → add smart wait or adjust assertion timeout
        - Page state is wrong → check flow, add wait for navigation
        - Test data conflict → generate new data (unique)
     3. Edit the code with replace_file_content / multi_replace_file_content
     4. Rerun the test
   ```3. **Verify stability** — run the test **twice in a row** PASS:```bash
   # Playwright
   npx playwright test <test_file> --repeat-each=2

   # Pytest
   python -m pytest <test_file> --count=2
   ```4. **⚠️ Rule E3:** DO NOT ask the user during the error fixing process. Only ask when:
   - URL is blocked / needs captcha
   - Business logic conflicts (unclear expected behavior)
   - 5 auto-heal rounds have passed but still failed

### Step 5: Cleanup & Delivery

1. **Code cleanup** (required before handover):
   - [ ] Delete`console.log()` / `print()`/debug log
   - [ ] Delete unused locator
   - [ ] Delete commented-out code
   - [ ] No more`waitForTimeout()` / `Thread.sleep()`- [ ] Import is not redundant

2. **Update artifact`task.md`** with results:```markdown
## Result
   - ✅ Pages created: LoginPage, DashboardPage
   - ✅ Tests created: login.spec.ts
   - ✅ Test status: 2/2 PASS (stable)
   - 📊 Locators collected: 8 elements, all verified
   ```3. **Report** to user:
   - List of created files
   - PASS/FAIL test number
   - Locator collection table (for user reference)
   - Known limitations (if any)

## Output

- **Artifact`task.md`** — progress checklist + results
- **Page Object classes** — 1 file per page/screen, locators verified from DOM
- **Test classes** — complete automation script, PASS tested
- **Locator Collection table** — all collected elements + primary/fallback locators
- **Evidence screenshots** — taken at important milestones