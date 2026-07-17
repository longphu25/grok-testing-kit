# Prompt Templates

Reusable prompt templates for common QA automation tasks. The agent can refer to it when it needs to format the output or when the user provides unclear requirements.

---

## 1. Test Case Generation

```
Analyze the following requirements and create test cases:

**Requirement:** [Description of requirement]

**Output format:**
| TC ID | Test Case Title | Precondition | Steps | Expected Result | Priority | Type |

**Requirement:**
- Includes positive, negative, boundary, edge cases
- Use English for description
- Priority: High / Medium / Low
- Type: Positive / Negative / Boundary / Edge
```

---

## 2. Automation Script Generation

```
Convert the following test case into automation script:

**Test Case:** [TC content]
**Framework:** [Selenium Java / Playwright TypeScript]
**Pattern:** Page Object Model

**Output:**
1. Page Object class(es)
2. Test class
3. Test data (if necessary)

**Rules:**
- Smart waits only (no hard sleep)
- Random test data with prefix + timestamp
- Assertions are clear
```

---

## 3. API Test Generation

```
Generate API tests from the Swagger specification:

**Swagger URL:** [URL]
**Endpoint(s):** [Endpoint to test]
**Framework:** REST Assured + TestNG

**Include:**
- Happy path (200 OK)
- Validation errors (400)
- Authentication (401/403)
- Not found (404)
- Boundary values
- Schema validation
```

---

## 4. Locator Generation

```
Inspect element and generate stable locator:

**Element:** [Description of element to find]
**Page URL:** [URL]
**Tool:** [Selenium / Playwright]

**Output:**
- Primary Locator (primary)
- Backup locator (fallback)
- Explain the reason for choosing the locator
```

---

## 5. Flaky Test Analysis

```
Analyze flaky tests and suggest fixes:

**Test file:** [Path to test]
**Symptoms:** [Description of flaky behavior]

**Analysis:**
1. Root cause
2. Pattern detection (timing, data, environment, selector)
3. Suggest specific fixes
4. Code fix
```

---

## 6. Test Data Generation

```
Sinh test data cho module:

**Module:** [Module name]
**Fields:** [List of fields needing data]

**Include:**
- Valid data (happy path)
- Invalid data (negative)
- Boundary values (min, max, empty, null)
- Special characters
- Format: JSON / CSV / Excel
```
