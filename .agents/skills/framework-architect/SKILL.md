---
name: framework-architect
description: Complete design and scaffold automation framework skills for Playwright, Selenium, and Appium — including project structure, base classes, config management, reporting, and CI/CD integration.
---

# Framework Architect

## Description

Specialized skills help agents design, scaffold and deploy automation frameworks from scratch. Cross-platform support (Web, Mobile, API) with the most popular frameworks.

Agents can:

- Design project structure according to best practices
- Generate base classes, config management, driver/browser management
- Integrated reporting (Allure, HTML Report, Playwright Report)
- Configure CI/CD pipeline (GitHub Actions, GitLab CI, Jenkins)
- Generate templates Page Object Model, fixtures, helpers
- Create configuration files (package.json, pom.xml, build.gradle, playwright.config.ts)

---

## When to Use

Use this skill when:

- User requests to create/design a new automation framework
- User needs scaffold project structure for test automation
- User wants to standardize the current framework
- Users need to integrate reporting or CI/CD into the framework
- User asks about best practices for framework design

Trigger keywords: "create framework", "design framework", "scaffold project", "design framework", "create new project"

---

## Supported Stacks

### 🌐 Web Automation

| Stack | Language | Runner | Report | Build Tools |
|---|---|---|---|---|
| **Playwright + TypeScript** | TypeScript | Playwright Test | HTML Report, Allure | npm |
| **Playwright + Java** | Java | TestNG/JUnit5 | Allure Report | Maven/Gradle |
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

## Framework Components

Each framework MUST include the following components (customized by stack):

### 1. Project Structure (Mandatory)
- Clear directory structure, separating pages/tests/utils/config
- README.md file instructions for setup + running tests
- The appropriate .gitignore file

### 2. Configuration Management (Mandatory)
- Manage environment (dev/staging/prod) via config file or .env
- Centralized config — do not hardcode values in test
- Sensitive data (credentials) via environment variables, DO NOT commit to repo

### 3. Browser / Driver Management (Mandatory)
- **Playwright:** playwright.config.ts / conftest.py with browser setup
- **Selenium:** WebDriverManager or Driver Factory pattern
- **Appium:** Desired Capabilities factory, Appium server config

### 4. Base Classes (Mandatory)
- Base Page — contains common methods (wait, click, type, screenshot)
- Base Test — contains setup/teardown, test lifecycle hooks
- No hardcode waits — only use smart waits

### 5. Page Object Model (Mandatory)
- Each page/screen → 1 Page class
- Locators are declared at the top of the class, not inline in the test
- Methods describe user behavior (not DOM manipulation)

### 6. Test Data Management (Mandatory)
- Data factory / builder pattern for test data
- Data external (JSON/YAML/CSV) for data-driven tests
- Data unique + traceable (timestamp/random prefix)
### 7. Utilities (Mandatory)
- Wait helpers (smart waits, custom conditions)
- Screenshot utilities (capture on failure)
- Logger (structured logging, do not use print/console.log)
- Date/Time helpers, String generators

### 8. Reporting (Mandatory)
- Integrate at least 1 reporting tool
- Screenshot attached on failure
- Test execution summary (pass/fail/skip counts)

### 9. CI/CD Pipeline (Optional — but recommended)
- GitHub Actions / GitLab CI / Jenkins pipeline template
- Parallel execution config
- Artifact upload (reports, screenshots)

---

## Project Structure Templates

### Playwright + TypeScript```
project-root/
├── playwright.config.ts        # Playwright configuration
├── package.json                # Dependencies + scripts
├── .env.example                # Environment template
├── .gitignore
├── README.md
├── src/
│   ├── pages/                  # Page Object classes
│   │   ├── base.page.ts        # Base page (common methods)
│   │   ├── login.page.ts
│   │   └── dashboard.page.ts
│   ├── fixtures/               # Custom fixtures
│   │   ├── auth.fixture.ts     # Authentication fixture
│   │   └── base.fixture.ts     # Extended test with all fixtures
│   ├── utils/                  # Helpers & utilities
│   │   ├── test-data.ts        # Data generators
│   │   ├── env.config.ts       # Environment config reader
│   │   └── helpers.ts          # Common helper functions
│   └── tests/                  # Test specs
│       ├── auth/
│       │   └── login.spec.ts
│       └── dashboard/
│           └── dashboard.spec.ts
├── test-data/                  # External test data (JSON/YAML)
│   └── users.json
└── .github/
    └── workflows/
        └── playwright.yml      # CI pipeline
```

### Selenium + Java (Maven + TestNG)

```
project-root/
├── pom.xml                     # Maven config + dependencies
├── testng.xml                  # TestNG suite config
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── main/java/
│   │   └── com/project/
│   │       ├── pages/          # Page Object classes
│   │       │   ├── BasePage.java
│   │       │   ├── LoginPage.java
│   │       │   └── DashboardPage.java
│   │       ├── drivers/        # Driver management
│   │       │   └── DriverFactory.java
│   │       ├── config/         # Configuration
│   │       │   └── ConfigReader.java
│   │       └── utils/          # Utilities
│   │           ├── WaitHelper.java
│   │           ├── ScreenshotUtil.java
│   │           └── TestDataGenerator.java
│   └── test/java/
│       └── com/project/
│           ├── base/
│           │   └── BaseTest.java
│           └── tests/
│               ├── LoginTest.java
│               └── DashboardTest.java
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── selenium.yml
```

### Appium + Java (Maven + TestNG)

```
project-root/
├── pom.xml
├── testng.xml
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── main/java/
│   │   └── com/project/
│   │       ├── screens/        # Screen Object classes (mobile POM)
│   │       │   ├── BaseScreen.java
│   │       │   ├── LoginScreen.java
│   │       │   └── HomeScreen.java
│   │       ├── drivers/        # Appium driver management
│   │       │   ├── AppiumDriverFactory.java
│   │       │   └── CapabilitiesManager.java
│   │       ├── config/
│   │       │   └── AppConfig.java
│   │       └── utils/
│   │           ├── MobileGestures.java    # Swipe, scroll, tap
│   │           ├── ScreenshotUtil.java
│   │           └── TestDataGenerator.java
│   └── test/java/
│       └── com/project/
│           ├── base/
│           │   └── BaseTest.java
│           └── tests/
│               ├── LoginTest.java
│               └── HomeTest.java
├── apps/                       # APK/IPA files
│   └── .gitkeep
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── appium.yml
```

### Playwright + Python (Pytest)

```
project-root/
├── playwright.config.py # Pytest-playwright config (if available)
├── pyproject.toml              # Python project config
├── requirements.txt            # Dependencies
├── conftest.py                 # Root fixtures + browser setup
├── .env.example
├── .gitignore
├── README.md
├── src/
│   ├── pages/
│   │   ├── base_page.py
│   │   ├── login_page.py
│   │   └── dashboard_page.py
│   ├── utils/
│   │   ├── config.py           # Env config reader
│   │   ├── test_data.py        # Data generators
│   │   └── helpers.py
│   └── tests/
│       ├── conftest.py         # Test-level fixtures
│       ├── test_login.py
│       └── test_dashboard.py
├── test-data/
│   └── users.json
└── .github/
    └── workflows/
        └── playwright.yml
```---

## Design Principles

1. **DRY (Don't Repeat Yourself)** — Each logic is only written once, reused through Base classes and Utils
2. **Single Responsibility** — Each class/module does one thing (Page only contains UI interaction, Test only contains test logic)
3. **Open/Closed** — Framework is easy to expand (add pages, add tests) without changing the core
4. **Configuration over Code** — Env, browser, timeout... managed via config, no hardcode
5. **Fail Fast, Log Rich** — Screenshot on failure, structured logging, clear assertion messages

---

## Anti-Patterns (FORBIDDEN)

| ❌ Anti-Pattern | ✅ Correct way |
|---|---|
| Hardcode URL/credentials in code | Read from .env or config file |
| Locator inline in test | Declared in Page class |
|`Thread.sleep()` / `waitForTimeout()` | Smart waits (`expect()`, `WebDriverWait`) |
| Global mutable state | Isolated fixtures/setup per test |
| Monolithic test file (1 file 500+ lines) | Separate by module/feature |
|`System.out.println()` / `console.log()`| Logger framework (Log4j, winston, logging) |

---

## Rules References

Agent MUST comply with detailed rules:

-`.agents/rules/automation_rules.md` — General automation best practices
- `.agents/rules/locator_strategy.md` — Locator selection priority
- `.agents/rules/playwright_rules.md` — Playwright-specific rules
- `.agents/rules/selenium_rules.md` — Selenium-specific rules
- `.agents/rules/appium_rules.md` — Appium mobile automation rules
