---
name: test-data-generator
description: Skill in generating structured, unique, traceable test data for automation tests, including positive, negative, boundary and edge cases.
---

# Test Data Generator

Purpose: Generate reliable test data for automation tests.

---

## When to Use

Use this skill when:

- Creating test data for new test cases
- Generating boundary and edge case data
- Setting up data-driven tests
- Creating API request payloads

---

## Responsibilities

Generate test data for:

- Registration forms
- Login credentials
- Form submissions
- API payloads
- Search queries
- File uploads

---

## Data Rules

All generated data must be:

- **Unique** — No duplication within test suite
- **Deterministic** — Same seed produces same data (when needed)
- **Traceable** — Can identify which test generated it

---

## Unique Data Pattern

Recommended format:

```
<prefix>_<testName>_<timestamp>
```

Examples:

```
auto_register_20260402133000
test_login_1712024100
```

---

## Common Data Types

### Email
```
auto_<testName>_<timestamp>@test.com
```
Example: `auto_register_20260402@test.com`

### Username
```
user_<testName>_<timestamp>
```
Example: `user_login_20260402133000`

### Phone
```
Random 10-digit number starting with valid prefix
```
Example: `0912345678`

### Password
```
Mix of uppercase, lowercase, digits, special chars
```
Example: `Test@12345`

---

## Data Categories

### Positive Data (Happy Path)
- Valid format, within constraints
- All required fields filled
- Standard business values

### Negative Data
- Missing required fields
- Invalid format (wrong email, short password)
- Invalid characters
- Already existing values (duplicate check)

### Boundary Values
- Minimum length (e.g., 1 character)
- Maximum length (e.g., 255 characters)
- Min + 1, Max - 1
- Empty string vs null
- Zero, negative numbers

### Edge Cases
- Unicode / special characters
- Very long strings
- SQL injection patterns (for security testing)
- HTML tags in text fields
- Leading/trailing whitespace

---

## Constraints

Test data must:

- Respect field validation rules (from DOM inspection)
- Match input format (date format, phone format)
- Avoid duplication across test runs
- Not contain real PII (personal data)

---

## Output Format

Provide data in structured format:

```json
{
  "positive": [
    { "email": "auto_tc01_20260402@test.com", "password": "Test@12345" }
  ],
  "negative": [
    { "email": "", "password": "Test@12345", "expectedError": "Email is required" },
    { "email": "invalid-email", "password": "Test@12345", "expectedError": "Invalid email format" }
  ],
  "boundary": [
    { "email": "a@b.co", "password": "12345678", "note": "Min length" }
  ]
}
```

---

## Multi-Step Data Pipeline (Cross-Module)

> Expanding the problem: test data needs to go through **many serial modules** to create complete data for the final module.

### When to use

- Feature to go through chain of N modules (eg: Partner → Payment → Tax → Minutes)
- The following data module **depends** on the previous module's output (Reference fields)
- Need to create real data on the system via browser

### Data Chain Pattern

```
Module 1 → Output: {id_1, code_1}
    ↓ (Reference)
Module 2 → Input: {id_1} → Output: {id_2}
    ↓ (Reference)
Module 3 → Input: {id_1, id_2} → Output: {id_3}
    ↓ (Reference)
Module N → Input: {id_1..id_N-1} → Output: Final Result
```

### Field Classification

| Field type | Description | How to generate data |
|-----------|-------|----------------|
| **Dimension field** | Values ​​belonging to the combined dimension in the matrix | Get exactly from the combo set — NOT random |
| **Supporting field** | Required but not dimension | Random + unique + traceable |
| **Reference field** | ID/code from previous output module | Copy from previous output module in string |
| **Computed field** | Self-adjective formula/business rules | Calculated by formula — must verify |

### Data Chain Tracing Format

```
auto_combo{XX}_{module_short}_{timestamp}
```

Example for combo 01:```
Module 1: partner_name  = "auto_c01_partner_1712049200"
Module 2: payment_desc  = "auto_c01_payment_1712049200"
Module 3: tax_note      = "auto_c01_tax_1712049200"
→ Can trace: what data does combo 01 create in each module
```

---

## Combinatorial Data Generation

> Generate data sets for **multi-dimensional combination matrix** — each combination set = 1 complete data set.

### When to use

- There is already a combination matrix (from`/generate-cross-module-test-plan`)
- Need to generate N data sets corresponding to N combination sets
- Each data set must have expected output (template, formula, computed values)

### Combinatorial Data Structure

```json
{
  "combination_id": "COMBO_01",
  "dimensions": {
    "D1": "value_from_matrix",
    "D2": "value_from_matrix"
  },
  "module_data": {
    "module_1": { "field1": "...", "field2": "..." },
    "module_2": { "ref_from_module1": "...", "field3": "..." }
  },
  "expected_output": {
    "template": "EXPECTED_TEMPLATE_CODE",
    "formula": "Amount × Rate",
    "computed_values": { "total": 110000000 }
  }
}
```

### Rules cho Combinatorial Data

| # | Rule |
|---|-------|
| 1 | Dimension values ​​MUST be 100% correct relative to the matrix — NOT random |
| 2 | Each combo uses its own data (unique per combo) |
| 3 | Computed values ​​must be correct according to formula |
| 4 | Each combo MUST have an expected output |
| 5 | Traceable: prefix`auto_combo{XX}` |

### Workflow reference

- `/generate-cross-module-test-plan`→ Generate combination matrix (input for this skill)
-`/generate-combinatorial-test-data`→ Main workflow uses this skill for combinatorial data

---

## Rules References

- `.agents/rules/automation_rules.md` — Test data generation rules (Section 2)