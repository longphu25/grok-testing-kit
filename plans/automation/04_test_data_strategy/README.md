# Step 4: Data Strategy (Test Data Strategy)

**Workflow:** `/generate-automation-from-testcases`(continued)
**Skill:**`/qa-automation-engineer` + `/test-data-generator`

---

## Purpose

Test automation often flakes if using hardcoded data. This step requires AI to design a traceable **random data generator** (Faker/Random) so that the test runs multiple times / in parallel without duplication.

## How to use

1. Send files`prompt.txt`for AI.
2. AI will generate:
   - Class Utils/DataProvider contains the function generate data
   - Instructions for integration into Test Cases
3. Review and integrate into the project → go to Step 5.

## Standard data format

```
[Prefix]_[TestName]_[Timestamp]_[Random]
```

For example:`auto_createCustomer_20260402_A3F2@test.com`

→ Look at the database and immediately know: data comes from testing`createCustomer`born at`2026-04-02`.

## Note

- Each test method has **separate** data → runs in parallel safely.
- Use Faker library for realistic data (person's name, address, phone number...).
- Comply with internal rules`.agents/rules/automation_rules.md` (Section 2: Test Data).
