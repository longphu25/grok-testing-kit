# General Rules for QA Automation

> Applies to all automation testing tasks, regardless of framework (Playwright, Selenium, Appium).

## 1. Architecture & Framework

- Required to use the **Page Object Model (POM)** model.
- Clear separation:
  - **Page classes:** Declare locators + UI interaction methods
  - **Test classes:** Contains test logic + assertions
  - **Test data:** Separated from functional code (JSON, DataProvider, Utils)
- Assertions are only placed in Test classes, NOT in Page classes.

## 2. Generate Test Data (Test Data)

- All fields requiring uniqueness (Email, Username, Customer Code...) **must be animated**, not hardcoded.
- Use UUID, Timestamp or Faker library.
- Data must be **traceable** — look at the DB and immediately know which test created:```
  Format: [prefix]_[testName]_[timestamp]_[random]
For example: auto_createCustomer_20260402_A3F2@test.com
  ```- Support running parallel: each test method has separate data, no conflict.

## 3. Code Quality

- No duplicate logic — create helper methods for repeated actions.
- Code must be simple, easy to read, and easy to maintain.
- Before delivering code:
  - Delete everything`console.log`, `System.out.println`, `print()`generated when debugging
  - Delete commented code (`//`, `/* */`)
  - Delete unused locators/variables (unused code)

## 4. File & Folder Management

- DO NOT automatically delete source files without confirming with the user.
- Check existing folder structure before creating new files — avoid duplicates.
- Place files in the correct folder according to project architecture (see`plan/automation/0_project_architecture`).

## 5. Naming Rules

### Java

| Ingredients | Rules | Example |
|---|---|---|
| Page class | PascalCase + suffix`Page` | `LoginPage.java`, `CartPage.java`|
| Test class | PascalCase + suffix`Test` | `LoginTest.java`, `CartTest.java`|
| Test method | Start with`test`+ behavior description |`testLoginWithValidCredentials()`|
| Variable Locator | lowerCamelCase + element description suffix |`loginButton`, `usernameInput`|
| Utils class | PascalCase + function description |`DataGenerator.java`, `WaitHelper.java`|

### TypeScript / Playwright

| Ingredients | Rules | Example |
|---|---|---|
| Page class | PascalCase + suffix`Page` | `LoginPage.ts`, `CartPage.ts` |
| Test file | kebab-case + `.spec.ts` | `login.spec.ts`, `cart.spec.ts` |
| Test blocks | `test('behavior description')` | `test('successful login')`|
| Variable Locator | lowerCamelCase or readonly |`readonly loginButton`|
| Utils | PascalCase or kebab-case |`DataGenerator.ts`, `data-generator.ts`|

## 6. Assertions (Check Results)

- Each test case **MANDATORY** has at least 1 assertion at the end.
- There should be alternating assertions at important steps.
- Assert must clearly describe expected behavior:```java
  // Java/TestNG
  Assert.assertTrue(dashboardPage.isDisplayed(), "Dashboard phải hiển thị sau khi đăng nhập");
  ```
  ```typescript
  // Playwright
  await expect(page.getByText('Đăng nhập thành công')).toBeVisible();
  ```## 7. Test Independence

- Each test case must be **independent** — not dependent on other test results.
- Clear setup/teardown (`@BeforeMethod/@AfterMethod`or`beforeEach/afterEach`).
- Do not share state between test methods.