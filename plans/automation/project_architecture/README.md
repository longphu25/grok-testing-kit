# Master Framework for E2E Automation (Generalized)

**Workflow:** `/generate-automation-from-testcases`or`/generate-automation-from-ui-flow`
**Skill:** `/qa-automation-engineer` + `/framework-architect`

---

## Target

Build an Automation system that is scalable, easy to maintain and provides professional reporting. Instead of "writing single tests", we build a solid **Original Framework** from scratch.

## How to use

Provide the appropriate architecture for the AI ​​**in Step 0/1** so that the AI ​​knows where the source code files are located.
- **New Project:** AI uses the template below to scaffold.
- **Available Project:** Describes the current structure, AI will follow.

---

## Supported Stacks

### 🌐 Web Automation

| Stack | Language | Runner | Report | Build Tools |
|---|---|---|---|---|
| **Playwright + TypeScript** | TypeScript | Playwright Test | HTML Report, Allure | npm |
| **Playwright + Python** | Python | Pytest | Allure, pytest-html | pip |
| **Selenium + Java** | Java | TestNG | Allure, ExtentReports | Maven/Gradle |
| **Selenium + Python** | Python | Pytest | Allure, pytest-html | pip |

### 📱 Mobile Automation

| Stack | Language | Runner | Report | Build Tools |
|---|---|---|---|---|
| **Appium + Java** | Java | TestNG | Allure, ExtentReports | Maven/Gradle |
| **Appium + Python** | Python | Pytest | Allure, pytest-html | pip |

### 🔌 API Automation

| Stack | Language | Runner | Report |
|---|---|---|---|
| **REST Assured** | Java | TestNG | Allure |
| **Playwright API** | TypeScript | Playwright Test | HTML Report |
| **Requests + Pytest** | Python | Pytest | Allure |

---

## Standard Architecture

### 1. Playwright + TypeScript

```text
project-root/
├── playwright.config.ts          # Playwright configuration
├── package.json                  # Dependencies + npm scripts
├── .env.example # Environment template (does NOT contain actual credentials)
├── .gitignore
├── README.md
├── src/
│   ├── pages/                    # Page Object classes
│   │   ├── base.page.ts          # Base page — common methods (wait, click, type, screenshot)
│   │   ├── login.page.ts
│   │   └── dashboard.page.ts
│   ├── fixtures/                 # Custom fixtures
│   │   ├── auth.fixture.ts       # Authentication fixture
│   │   └── base.fixture.ts       # Extended test with all fixtures
│   ├── utils/                    # Helpers & utilities
│   │   ├── test-data.ts          # Data generators (unique + traceable)
│   │   ├── env.config.ts         # Environment config reader
│   │   └── helpers.ts            # Common helper functions
│   └── tests/                    # Test specs (grouped by feature)
│       ├── auth/
│       │   └── login.spec.ts
│       └── dashboard/
│           └── dashboard.spec.ts
├── test-data/                    # External test data (JSON/YAML)
│   └── users.json
└── .github/
    └── workflows/
        └── playwright.yml        # CI pipeline template
```

### 2. Playwright + Python (Pytest)

```text
project-root/
├── pyproject.toml                # Python project config
├── requirements.txt              # Dependencies
├── conftest.py                   # Root fixtures + browser setup
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── pages/
│   │   ├── base_page.py          # Base page — common methods
│   │   ├── login_page.py
│   │   └── dashboard_page.py
│   ├── utils/
│   │   ├── config.py             # Env config reader
│   │   ├── test_data.py          # Data generators
│   │   └── helpers.py
│   └── tests/
│       ├── conftest.py           # Test-level fixtures
│       ├── test_login.py
│       └── test_dashboard.py
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── playwright.yml
```

### 3. Selenium + Java (Maven + TestNG)

```text
project-root/
├── pom.xml                       # Maven config + dependencies
├── testng.xml                    # TestNG suite config
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── main/java/
│   │   └── com/project/
│   │       ├── pages/            # Page Object classes
│   │       │   ├── BasePage.java         # Base page — common methods
│   │       │   ├── LoginPage.java
│   │       │   └── DashboardPage.java
│   │       ├── drivers/          # Driver management
│   │       │   └── DriverFactory.java
│   │       ├── config/           # Configuration
│   │       │   └── ConfigReader.java
│   │       └── utils/            # Utilities
│   │           ├── WaitHelper.java
│   │           ├── ScreenshotUtil.java
│   │           └── TestDataGenerator.java
│   └── test/java/
│       └── com/project/
│           ├── base/
│           │   └── BaseTest.java         # Test lifecycle hooks
│           └── tests/
│               ├── LoginTest.java
│               └── DashboardTest.java
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── selenium.yml
```

### 4. Selenium + Python (Pytest)

```text
project-root/
├── pyproject.toml
├── requirements.txt
├── conftest.py                   # Root fixtures + driver setup
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── pages/
│   │   ├── base_page.py
│   │   ├── login_page.py
│   │   └── dashboard_page.py
│   ├── drivers/
│   │   └── driver_factory.py     # WebDriver factory
│   ├── utils/
│   │   ├── config.py
│   │   ├── test_data.py
│   │   ├── wait_helper.py
│   │   └── screenshot_util.py
│   └── tests/
│       ├── conftest.py
│       ├── test_login.py
│       └── test_dashboard.py
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── selenium.yml
```

### 5. Appium + Java (Maven + TestNG)

```text
project-root/
├── pom.xml
├── testng.xml
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── main/java/
│   │   └── com/project/
│ │ ├── screens/ # Screen Objects (equivalent to Page Objects)
│   │       │   ├── BaseScreen.java       # Base screen — common mobile methods
│   │       │   ├── LoginScreen.java
│   │       │   └── HomeScreen.java
│   │       ├── drivers/          # Appium driver management
│   │       │   ├── AppiumDriverFactory.java
│   │       │   └── CapabilitiesManager.java
│   │       ├── config/
│   │       │   └── AppConfig.java
│   │       └── utils/
│   │           ├── MobileGestures.java   # Swipe, scroll, tap helpers
│   │           ├── ScreenshotUtil.java
│   │           └── TestDataGenerator.java
│   └── test/java/
│       └── com/project/
│           ├── base/
│           │   └── BaseTest.java
│           └── tests/
│               ├── LoginTest.java
│               └── HomeTest.java
├── apps/                         # APK/IPA files
│   └── .gitkeep
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── appium.yml
```

### 6. Appium + Python (Pytest)

```text
project-root/
├── pyproject.toml
├── requirements.txt
├── conftest.py                   # Root fixtures + Appium driver setup
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── screens/
│   │   ├── base_screen.py
│   │   ├── login_screen.py
│   │   └── home_screen.py
│   ├── drivers/
│   │   ├── appium_driver_factory.py
│   │   └── capabilities_manager.py
│   ├── utils/
│   │   ├── config.py
│   │   ├── test_data.py
│   │   ├── mobile_gestures.py
│   │   └── screenshot_util.py
│   └── tests/
│       ├── conftest.py
│       ├── test_login.py
│       └── test_home.py
├── apps/
│   └── .gitkeep
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── appium.yml
```

### 7. REST Assured (Java + TestNG)

```text
project-root/
├── pom.xml
├── testng.xml
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── main/java/
│   │   └── com/project/
│   │       ├── api/              # API client classes
│   │       │   ├── BaseApi.java          # Base API — common request methods
│   │       │   ├── AuthApi.java
│   │       │   └── UserApi.java
│   │       ├── models/           # POJO / DTO classes
│   │       │   ├── UserRequest.java
│   │       │   └── UserResponse.java
│   │       ├── config/
│   │       │   └── ApiConfig.java
│   │       └── utils/
│   │           ├── TestDataGenerator.java
│   │           └── JsonHelper.java
│   └── test/java/
│       └── com/project/
│           ├── base/
│           │   └── BaseApiTest.java
│           └── tests/
│               ├── AuthApiTest.java
│               └── UserApiTest.java
├── test-data/
│   ├── payloads/                 # Request body templates (JSON)
│   │   └── create_user.json
│   └── schemas/                  # JSON Schema validation
│       └── user_response_schema.json
└── .github/
    └── workflows/
        └── api-tests.yml
```

---

## Component Checklist (Required)

Every framework MUST include the following components:

| # | Components | Required | Description |
|---|-----------|----------|-------|
| 1 | **Project Structure** | ✅ | Clear directory, separated pages/tests/utils/config |
| 2 | **Config Management** | ✅ | Environment passed`.env`+ config file — NO hardcode |
| 3 | **Browser / Driver Management** | ✅ | Factory pattern for browser/driver setup |
| 4 | **Base Classes** | ✅ | BasePage/BaseScreen contains common methods |
| 5 | **Page Object Model** | ✅ | Each page → 1 class, locators declared at the top of class |
| 6 | **Test Data Management** | ✅ | Data factory + external data (JSON/YAML) + unique/traceable |
| 7 | **Utilities** | ✅ | Wait helpers, screenshot utils, loggers, string generators |
| 8 | **Reporting** | ✅ | At least 1 report tool + screenshot on failure |
| 9 | **CI/CD Pipeline** | 🟡 Recommended | GitHub Actions / GitLab CI / Jenkins templates |

---

## Design Principles

1. **DRY** — Each logic is only written once, reused through Base classes and Utils
2. **Single Responsibility** — Page only contains UI interaction, Test only contains test logic
3. **Open/Closed** — Easy to expand (add pages, add tests) without changing the core
4. **Configuration over Code** — Env, browser, timeout management via config, no hardcode
5. **Fail Fast, Log Rich** — Screenshot on failure, structured logging, clear assertion messages

---

## Anti-Patterns (PROHIBITED)

| ❌ Anti-Pattern | ✅ Correct way |
|---|---|
| Hardcode URL/credentials in code | Read words`.env`or config file |
| Locator inline in test | Declared in Page class |
|`Thread.sleep()` / `waitForTimeout()` | Smart waits (`expect()`, `WebDriverWait`) |
| Global mutable state | Isolated fixtures/setup per test |
| Monolithic test file (1 file 500+ lines) | Separate by module/feature |
|`System.out.println()` / `console.log()`| Logger framework (Log4j, winston, logging) |
| Guess the locator doesn't inspect DOM | Open browser inspect actual DOM |

---

## Reference

- **Detailed skills:**`.agents/skills/framework-architect/SKILL.md`
- **Rules:** `.agents/rules/automation_rules.md`, `.agents/rules/locator_strategy.md`
- **Workflow scaffold:** `/generate-automation-framework`
