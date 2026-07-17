---
name: generate-test-data
description: Generate structured, unique, traceable test data for test cases. Supports UI forms, API payloads, data-driven tests.
---

# /generate-test-data — Generate Structured Test Data

> User provides feature/module or test case that needs to generate data.
> AI analyzes fields, constraints, generates full data sets (positive, negative, boundary, edge cases) with ready-to-use format.

> **MANDATORY:** Before starting, MUST load and read carefully:
> - **Skill:**`.agents/skills/test-data-generator/SKILL.md`— Data generation rules
> - **Rule:**`.agents/rules/automation_rules.md` — Section Test Data

---

## Input needed from User

| Input | Required | Description |
|-------|----------|-------|
| Features / Modules | ✅ | For example: "Registration form", "API Create User", "Login page" |
| Fields need data | ⚠️ Should have | List of fields + constraints. If not → AI analyzes itself from DOM/Spec |
| Page URL / Swagger spec | ❌ | If yes → AI inspect DOM/Spec to get the correct validation rules |
| Test cases | ❌ | If there are test cases → AI generates data to match each TC |
| Output format | ❌ |`json`(default),`csv`, `markdown table`, `code`(TypeScript/Java/Python) |
| Data language | ❌ | Vietnamese / English (default: according to context) |

---

## Steps to perform

### Step 1: Analyze Fields & Constraints

1. **Identify the source of information:**

| Source | How to get | Priority |
   |-------|----------|-------|
   | User provided directly | Read from input | ⭐ Highest |
   | Actual DOM (UI form) |`browser_navigate` → `browser_snapshot`→ analyze input fields | ⭐ High |
   | Swagger/OpenAPI spec |`read_url_content`→ parse schema + constraints | ⭐ High |
   | Test cases already exist | Read test steps → extract fields | Average |
   | Guess from module name | Based on domain experience | ⭐ Lowest |

2. **For each field, determine:**

| Attribute | Example |
   |-----------|-------|
   | **Field name** | email, password, name, phone |
   | **Data type** | string, number, boolean, date, file, enum |
   | **Required?** | Required or optional |
   | **Validation rules** | min/maxLength, pattern/regex, format (email, phone), unique |
   | **Default value** | If there is |
   | **Enum values** | For example: status = ["AVAILABLE", "UNAVAILABLE"] |
   | **Dependency relationship** | For example:`password_confirm`must match`password` |

3. **Present the Fields table for the User to confirm (CHECKPOINT ⏸️):**

   ```markdown
   | # | Field | Type | Required | Constraints | Notes |
   |---|-------|------|----------|-------------|-------|
| 1 | email | string | ✅ | format: email, unique | Used to login |
   | 2 | password | string | ✅ | minLength: 6 | |
   | 3 | name | string | ✅ | maxLength: 100 | |
| 4 | phone | string | ❌ | pattern: /^0[0-9]{9}$/ | 10 digits, starting with 0 |
   ```

> Wait for the User to confirm or add constraints before going to Step 2.

---

### Step 2: Generate Test Data according to 4 Categories

For each identified field, generate data according to the following table:

#### 2A. Positive Data (Happy Path)

- Valid value, correct format, within constraints
- All required fields are filled in
- Data must be **unique + traceable**

**Format unique data:**
```
<prefix>_<testName>_<timestamp>
```
For example:
```
email:    auto_register_1712049200@test.com
username: user_login_1712049200
code:     TC_BOOK_1712049200
```

#### 2B. Negative Data

| Type | Description | Example |
|-------|--------|-------|
| **Missing required** | Leave the required field | blank`email: ""` |
| **Invalid format** | Sai format | `email: "not-an-email"`|
| **Invalid type** | Wrong data type |`price: "abc"`(expect number) |
| **Duplicate** | Value already exists |`email: "existing@test.com"`|
| **Invalid characters** | Unallowed character |`name: "<script>alert(1)</script>"`|
| **Wrong relationship** | Violation of the fields | relationship`password_confirm ≠ password` |

#### 2C. Boundary Values

| Type | Description | For example (field has minLength=6, maxLength=20) |
|-------|--------|-------|
| **Min** | Exactly equal to min |`"abcdef"`(6 chars) |
| **Min - 1** | Below min |`"abcde"`(5 chars) |
| **Max** | Exactly equal to max |`"a" * 20`(20 chars) |
| **Max + 1** | Exceeding max |`"a" * 21`(21 chars) |
| **Empty** | Empty string |`""`|
| **Zero** | Number 0 (for number fields) |`0`|
| **Negative** | Negative numbers (for number fields) |`-1` |

#### 2D. Edge Cases

| Type | Description | Example |
|-------|--------|-------|
| **Unicode** | Unicode special characters |`"Nguyen Van 🎉"`|
| **Very long** | Extremely long chain |`"a" * 10000`|
| **Whitespace** | Leading/trailing whitespace |`"  email@test.com  "` |
| **SQL injection** | Pattern SQL injection | `"'; DROP TABLE users; --"` |
| **HTML tags** | HTML trong text field | `"<b>bold</b><img src=x onerror=alert(1)>"`|
| **Null/undefined** | Null value |`null`|
| **Special numbers** | Special issue |`0.1 + 0.2`, `Number.MAX_SAFE_INTEGER`, `NaN`|
| **Date edge** | Special day |`"2024-02-29"`(leap year),`"2024-12-31"`, `"1970-01-01"` |

---

### Step 3: Packaging Output

Return results in the format User requested (default: JSON):

#### JSON Format (default)

```json
{
  "module": "User Registration",
  "totalDataSets": 15,
  "fields": ["email", "password", "name", "phone"],
  "positive": [
    {
      "id": "POS_01",
      "description": "Đăng ký thành công với tất cả fields hợp lệ",
      "data": {
        "email": "auto_register_1712049200@test.com",
        "password": "Test@12345",
        "name": "Auto User Register",
        "phone": "0912345001"
      },
      "expectedResult": "Tạo tài khoản thành công, status 201"
    }
  ],
  "negative": [
    {
      "id": "NEG_01",
      "description": "Email trống",
      "data": { "email": "", "password": "Test@12345", "name": "Test User" },
      "expectedResult": "Lỗi validation: Email is required",
      "targetField": "email",
      "negativeType": "missing_required"
    },
    {
      "id": "NEG_02",
      "description": "Email sai format",
      "data": { "email": "not-email", "password": "Test@12345", "name": "Test User" },
      "expectedResult": "Lỗi validation: Invalid email format",
      "targetField": "email",
      "negativeType": "invalid_format"
    }
  ],
  "boundary": [
    {
      "id": "BND_01",
      "description": "Password đúng min length (6 chars)",
      "data": { "email": "auto_bnd01_1712049200@test.com", "password": "Abc@12", "name": "Test User" },
      "expectedResult": "Thành công",
      "targetField": "password",
      "boundaryType": "min"
    }
  ],
  "edgeCases": [
    {
      "id": "EDGE_01",
      "description": "Name chứa Unicode English + emoji",
      "data": { "email": "auto_edge01_1712049200@test.com", "password": "Test@12345", "name": "Nguyễn Văn 🎉" },
      "expectedResult": "Thành công — hệ thống chấp nhận Unicode",
      "targetField": "name",
      "edgeType": "unicode"
    }
  ]
}
```

#### Format Markdown Table

```markdown
| ID | Category | Description | email | password | name | Expected Result |
|----|----------|-------------|-------|----------|------|-----------------|
| POS_01 | Positive | Registered successfully | auto_reg@test.com | Test@12345 | Auto User | 201 Created |
| NEG_01 | Negative | Empty email | (empty) | Test@12345 | Test User | 422: Email is required |
| BND_01 | Boundary | Password min length | auto_bnd@test.com | Abc@12 | Test User | 201 Created |
```

#### Format Code (TypeScript example)

```typescript
// test-data/registration.data.ts
export const registrationData = {
  positive: {
    email: `auto_register_${Date.now()}@test.com`,
    password: 'Test@12345',
    name: 'Auto User Register',
    phone: '0912345001',
  },
  negative: {
    emptyEmail: { email: '', password: 'Test@12345', name: 'Test' },
    invalidEmail: { email: 'not-email', password: 'Test@12345', name: 'Test' },
    shortPassword: { email: `auto_neg_${Date.now()}@test.com`, password: '123', name: 'Test' },
  },
  boundary: {
    minPassword: { email: `auto_bnd_${Date.now()}@test.com`, password: 'Abc@12', name: 'Test' },
    maxName: { email: `auto_bnd_${Date.now()}@test.com`, password: 'Test@12345', name: 'A'.repeat(100) },
  },
};
```

---

## Data Rules (REQUIRED)

| # | Rule | Description |
|---|-------|-------|
| 1 | **Unique** | No duplicates in test suite — use timestamp/random |
| 2 | **Traceable** | You can trace back which test generated the data — using prefix + test name |
| 3 | **No real PII** | DO NOT use real personal data (CCCD number, real email, real phone number) |
| 4 | **Respect constraints** | Data must comply with the analyzed validation rules |
| 5 | **Include expectedResult** | Each data set MUST have an expected result to use for assertion |
| 6 | **Deterministic when needed** | Same seed → same data (for reproducible tests) |

---

## STRICTLY PROHIBITED

| ❌ Do not do | ✅ Correct replacement |
|-------------------|-----------------| 
| Use placeholders (`valid email`, `valid code`) | Specific value:`auto_tc01@test.com`, `KH-2026-0012`|
| Hardcode data duplicates between tests | Random data with prefix + timestamp |
| Use real personal data | Simulated data:`auto_*@test.com`|
| Only generates positive data | All 4 categories are required: Positive + Negative + Boundary + Edge |
| Generate data without expected result | Each data set MUST state expected result |
| Guess validation rules don't check | Inspect DOM/Spec or ask User |
| Read`.env`to get credentials | Ask User or use placeholder`[FROM_ENV]` |

---

## Final checklist

- [ ] Fully analyzed fields + constraints
- [ ] User has confirmed the fields table before generating data
- [ ] Data has 4 categories (Positive, Negative, Boundary, Edge Cases)
- [ ] Each data set has a clear expected result
- [ ] Data unique + traceable (prefix + timestamp/random)
- [ ] Does not contain real PII
- [ ] Correct output format required by User (JSON/CSV/Markdown/Code)
- [ ] Boundary values are correct according to constraints (min, min-1, max, max+1)