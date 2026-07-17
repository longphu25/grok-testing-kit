---
name: generate-automation-framework
description: Design and complete scaffold automation framework. Supports Playwright, Selenium, Appium — Web, Mobile, API.
---

# Workflow: Automation Framework Design

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/framework-architect`** (in`.agents/skills/framework-architect/SKILL.md`) before starting. Additionally, refer to additional skills **`/qa-automation-engineer`** to understand general automation rules.

This workflow helps agents design, scaffold and deploy a complete automation framework from scratch, tailored to the specific needs of the project.

## ⚠️ Implementation principles

- **All output in English**
- **DO NOT guess** tech stack — must ask user for confirmation before scaffolding
- **MUST create artifact`task.md`** to track progress
- Each generated file must be **compiled/executable immediately** — no placeholders`// TODO`- Framework must comply with design principles in skill`/framework-architect`## Stacks support

| Platform | Stack | Language | Runner | Report |
|---|---|---|---|---|
| 🌐 Web | Playwright | TypeScript | Playwright Test | HTML Report, Allure |
| 🌐 Web | Playwright | Java | TestNG/JUnit5 | Allure |
| 🌐 Web | Playwright | Python | Pytest | pytest-html, Allure |
| 🌐 Web | Selenium | Java | TestNG | Allure, ExtentReports |
| 🌐 Web | Selenium | Python | Pytest | pytest-html, Allure |
| 📱 Mobile | Appium | Java | TestNG | Allure, ExtentReports |
| 📱 Mobile | Appium | Python | Pytest | Allure |
| 🔌 API | REST Assured | Java | TestNG | Allure |
| 🔌 API | Playwright API | TypeScript | Playwright Test | HTML Report |

## Steps to perform

### Step 1: Gathering requirements (Requirements Gathering — ⏸️ CHECKPOINT)

1. **Ask the user** for necessary information:

   | Question | Purpose | Default if not answered |
   |---|---|---|
   | What is the application to test? (Web / Mobile / API / Hybrid) | Select platform | Web |
   | Which framework? (Playwright / Selenium / Appium) | Select tools | Playwright |
   | Language? (TypeScript / Java / Python) | Select language | TypeScript (Playwright), Java (Selenium/Appium) |
   | Project name? | Name the folder |`automation-framework`|
   | Is a CI/CD pipeline needed? | Generate pipeline config | Yes (GitHub Actions) |
   | Reporting tool? | Report integration | Default by stack |
   | Is there parallel API testing? | Add API testing layer | No |
   | Parallel execution? | Config parallel | No |

2. **Reconfirm** with the user before scaffolding:```
📋 Summary of what the framework will create:
   - Platform: Web
   - Framework: Playwright
   - Language: TypeScript
   - Runner: Playwright Test
   - Report: HTML Report + Allure
   - CI/CD: GitHub Actions
   - Project name: my-automation
   
Can you confirm so I can start the scaffold?
   ```3. **Wait for user confirmation** before going to Step 2

### Step 2: Scaffold Project Structure (Foundation)

1. **Create artifact`task.md`** to follow the checklist:```markdown
   # Framework Setup Progress
- [x] Step 1: Collect requirements
   - [ ] Step 2: Scaffold project structure
   - [ ] Step 3: Generate base classes
   - [ ] Step 4: Generate example tests
   - [ ] Step 5: Configure reporting & CI/CD
   - [ ] Step 6: Verify & Deliver
   ```2. **Create project folder** according to the template in skill`/framework-architect`:
   - Refer to **Project Structure Templates** section in SKILL.md
   - Create entire folder + original configuration file

3. **Generate build configuration file** (depending on stack):

   **Playwright + TypeScript:**
   -`package.json` — dependencies: `@playwright/test`, devDependencies accordingly
   -`playwright.config.ts` — baseURL, viewport (1920x1080), timeout, retries, reporter
   - `tsconfig.json` — paths, strict mode
   - `.env.example` — template environment variables

   **Selenium + Java:**
   - `pom.xml` — dependencies: selenium-java, testng, webdrivermanager, allure-testng, log4j
   - `testng.xml` — suite configuration, listeners
   - `log4j2.xml` — logging configuration

   **Appium + Java:**
   - `pom.xml` — dependencies: appium-java-client, selenium-java, testng, allure
   - `testng.xml` — suite configuration
   - Capabilities config file (JSON/YAML) cho Android + iOS

   **Playwright + Python:**
   - `requirements.txt` — playwright, pytest, pytest-playwright, allure-pytest
   - `pyproject.toml` — pytest config, tool settings
   - `conftest.py`— root fixtures, browser setup

4. **Create appropriate .gitignore file** (node_modules, target, __pycache__, .env, reports...)
5. **Create README.md** with instructions:
   - Prerequisites (Node.js, Java, Python version)
   - Installation steps
   - How to run tests
   - Project structure overview
   - Conventions (naming, coding standards)

### Step 3: Generate Core Classes (Base Layer)

1. **Configuration Management:**

   | Stack | File | Content |
   |---|---|---|
   | Playwright TS |`src/utils/env.config.ts`| Read`.env`, export typed config object |
   | Selenium Java | `src/main/.../config/ConfigReader.java`| Read properties file, singleton pattern |
   | Appium Java |`src/main/.../config/AppConfig.java`| Read capabilities JSON, env variables |
   | Playwright Python |`src/utils/config.py`| Read`.env`equal`python-dotenv`|

2. **Browser / Driver Management:**

   | Stack | File | Content |
   |---|---|---|
   | Playwright TS |`playwright.config.ts` + fixtures | Browser config trong config, auth trong fixtures |
   | Selenium Java | `DriverFactory.java` | Factory pattern, WebDriverManager, thread-safe |
   | Appium Java | `AppiumDriverFactory.java` + `CapabilitiesManager.java` | Appium server URL, desired capabilities per device |
   | Playwright Python | `conftest.py` | Fixtures cho browser, context, page |

3. **Base Page / Screen class:**
   - Common methods: `navigate()`, `click()`, `type()`, `getText()`, `isVisible()`- Built-in smart waits (NO hard sleep)
   - Screenshot on failure
   - Logging each action

4. **Base Test class:**
   - Setup: initialize browser/driver, navigate to baseURL
   - Teardown: close browser/driver, capture screenshot if failed
   - Test lifecycle hooks (beforeAll, afterAll, beforeEach, afterEach)

5. **Utilities:**
   -`TestDataGenerator` — sinh email, username, phone unique + traceable
   - `WaitHelper`— custom wait conditions (if needed beyond built-in)
   -`ScreenshotUtil` — capture + attach to report
   - `Logger`— structured logging (Log4j / winston / Python logging)

### Step 4: Generate Example Tests (Validation Layer)

1. **Create at least 1 example Page Object:**
   -`LoginPage`(or`LoginScreen`for mobile) with actual locators + methods
   - Locator uses placeholder to clearly describe the need for replacement:```typescript
     // REPLACE: Update locator after inspecting actual DOM
     readonly usernameInput = this.page.getByLabel('Username');
     readonly passwordInput = this.page.getByLabel('Password');
     readonly loginButton = this.page.getByRole('button', { name: 'Login' });
     ```2. **Create at least 1 example Test:**
   -`LoginTest`— demo happy path (successful login)
   - Completely available: Arrange → Act → Assert
   - Assertion has a clear message
   - Test data using TestDataGenerator (if applicable)

3. **Create an example data-driven test** (if appropriate):
   - Read data from JSON/YAML/CSV file
   - Parameterized test with many data sets

### Step 5: Configure Reporting & CI/CD (Integration Layer)

1. **Reporting setup:**

   | Stack | Report | Configuration |
   |---|---|---|
   | Playwright TS | HTML + Allure |`reporter` trong playwright.config.ts |
   | Selenium Java | Allure | `allure-testng` dependency + `@Step`, `@Attachment` annotations |
   | Appium Java | Allure + ExtentReports | `allure-testng` + `ExtentManager` singleton |
   | Playwright Python | pytest-html + Allure | `conftest.py`hooks + pytest addopts |

   - Screenshot auto-attach on failure
   - Test step logging in report

2. **CI/CD Pipeline** (if user requests):

   **GitHub Actions template:**```yaml
   # Sinh file .github/workflows/<framework>.yml
   # Nội dung tùy stack: install deps → run tests → upload report
   ```- Install dependencies
   - Run tests (headless mode for CI)
   - Upload test report as artifact
   - Parallel execution (if required)
   - Environment variables from GitHub Secrets

3. **Docker support** (optional — only if user requests):
   - Dockerfile for test execution environment
   - docker-compose.yml if Selenium Grid / Appium server is needed

### Step 6: Verify & Deliver (Quality Gate)

1. **Check if the framework can be built:**```bash
   # Playwright TS
   npm install && npx playwright install && npx playwright test --list

   # Selenium Java
   mvn clean compile

   # Appium Java
   mvn clean compile

   # Playwright Python
   pip install -r requirements.txt && playwright install
   ```2. **Run example test** to verify the framework works:
   - If PASS → framework is ready
   - If FAIL due to missing app/URL → acceptable (note in README)
   - If FAIL is due to a framework code error → fix it immediately

3. **Review checklist** before handover:
   - [ ] Project structure is correct template
   - [ ] All declared dependencies
   - [ ] Config management active (read .env)
   - [ ] Base Page/Test has full common methods
   - [ ] POM pattern is followed
   - [ ] Smart waits — no hard sleep
   - [ ] Example test can run (or can run when there is an app)
   - [ ] README.md full instructions
   - [ ] .gitignore covers properly
   - [ ] Integrated reporting
   - [ ] No debug log, commented code, TODO placeholder

4. **Update`task.md`** with completed status

## Handle special situations

| Situation | How to handle |
|---|---|
| **User wants hybrid (Web + Mobile)** | Create shared layer (utils, config) + separate pages vs screens |
| **User wants hybrid (Web + API)** | Add parallel layer API (api helpers + api tests folder) |
| **User has an old framework that needs refactoring** | Read current code → propose migration plan → refactor each part |
| **User wants multi-browser** | Config parallel projects in playwright.config.ts or test suite XML |
| **User wants multi-device (Appium)** | CapabilitiesManager supports loading from JSON per device (Android/iOS) |
| **User doesn't know how to choose stack** | Suggestions based on: team skill, project type, CI infra |

## Output

- Full **Project structure** (all folders + files)
- **Build config** (package.json / pom.xml / requirements.txt)
- **Framework config** (playwright.config.ts / testng.xml / conftest.py)
- **Base classes** (BasePage, BaseTest, DriverFactory, ConfigReader)
- **Utilities** (TestDataGenerator, WaitHelper, ScreenshotUtil, Logger)
- **Example Page Object + Test** (LoginPage + LoginTest)
- **Reporting integration** (Allure / HTML Report)
- **CI/CD pipeline** (GitHub Actions template)
- **README.md** (setup guide + project overview)
- **Artifact`task.md`** (progress checklist)