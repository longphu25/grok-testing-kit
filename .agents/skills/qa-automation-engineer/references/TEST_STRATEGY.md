# Test Strategy

## Instructions for Use

This file defines the testing strategy for the project. Agents refer to this file to understand scope, priorities, and approach when generating test cases or automation scripts.

> ⚠️ **You need to update this file** for each specific project. Below is the template.

---

## Testing Objectives

- Ensure software quality through testing levels
- Detect errors early in the development lifecycle
- Maintain stable regression suite for CI/CD

## Scope of Testing

| Test Type | Apply | Tool/Framework |
|-----------|---------|----------------|
| UI Functional Testing | ✅ | Selenium / Playwright |
| API Testing | ✅ | REST Assured / Postman |
| Unit Testing | ✅ | JUnit / TestNG |
| Integration Testing | ✅ | TestNG + REST Assured |
| Performance Testing | ⬜ | JMeter/k6 |
| Security Testing | ⬜ | OWASP ZAP |
| Mobile Testing | ⬜ | Appium |

## Test Automation Strategy

### Framework Architecture
- **Design Pattern:** Page Object Model (POM)
- **Language:** Java
- **Test Runner:** TestNG
- **Build Tool:** Maven
- **Reporting:** Allure / ExtentReports

### Automation Scope
- Smoke tests: Cover the happy path of main functions
- Regression tests: Covers all passed test cases
- Data-driven tests: Use external data sources (Excel, CSV, JSON)

## Test Data Management

- Use random data with prefix + timestamp to make it traceable
- Separate test data from test logic
- Do not hard-code credentials in the code

## Execution Plan

| Phase | Description | Triggers |
|--------|--------|--------|
| Smoke Test | Happy main path | Each build |
| Regression | Full suite | Before release |
| Integration | API + UI | Daily |

## Test Environment

- Test runs on Staging environment
- CI/CD pipeline runs headless mode
- Local debug runs headed mode (viewport 1920x1080)