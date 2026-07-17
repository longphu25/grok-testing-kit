---
name: generate-testcases-from-requirements
description: Generate manual test cases quickly from requirements (QUICK mode — without going through the 6-step process).
---

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/rbt-manual-testing`** (in`.agents/skills/rbt-manual-testing/SKILL.md`) before starting this task. Use **Mode QUICK** of the skill.

# Workflow: Generate Manual Test Cases Quickly from Requirements

This workflow uses the skill's **Mode QUICK**`/rbt-manual-testing`to quickly generate test cases from existing requirements.

## ⚠️ Principle

- **Mode:** QUICK (1 turn only, no waiting for the user midway)
- Suitable for simple modules, requirements are clear
- If requirements are discovered to be too complex or ambiguous → **automatically switch to FULL RBT** and notify the user
- All output in **Vietnamese**

## Steps to perform

1. **Read and understand the requirements** provided by the user
2. **Identify main flows:** Happy Path, Negative Path, Boundary Cases, Edge Cases
3. **Apply automated test case design techniques:**
   - Equivalence Partitioning (EP)
   - Boundary Value Analysis (BVA)
   - Decision Table (if there are many rules)
   - State Transition (if there is a workflow)
4. **Field-Level Validation:**
   - List all input fields on form/UI
   - Generate validation TCs **separately for EACH field** according to specific characteristics (text, email, phone, date, number, dropdown, file upload, password...)
   - Apply **Field-Level Validation Table** in skill`/rbt-manual-testing`to choose the appropriate validation
   - **DO NOT** combine validation of multiple fields into 1 test case
5. **Generate test cases with complete fields:**
- TC ID (format:`[PROJECT]_[MODULE]_TC_[NUMBER]`)
   - Modules
   - Test Scenario / Test Case Title
   - Pre-conditions
   - Test Steps (numbered)
   - Expected Results (numbered accordingly)
   - Test Data (**must be specific**, no placeholder)
   - Priority (Critical / High / Medium / Low)
6. **Export to standard Markdown table**

## Output table

```
| TC ID | Module | Test Scenario | Pre-Condition | Test Steps | Test Data | Expected Result | Priority |
```

## Important rules

- Test Data must be specific:`test_login_01@domain.com`, not "valid email"
- Must include Positive, Negative, Boundary, and Edge cases
- Each input field must have its own validation TCs (do not combine multiple fields into 1 TC)
- TC ID follows a uniform format set by user convention or default`[PROJECT]_[MODULE]_TC_[NUMBER]`
- If there are too many TCs → divide into Part 1, Part 2 and ask the user

## When to switch to FULL RBT

Agent **automatically suggests switching modes** if it detects:
- Requirements are vague, need to ask Q&A
- Large scope (>3 modules)
- Complex business logic, many overlapping conditions
- User requests Traceability Matrix or Risk Assessment