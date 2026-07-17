# Step 3: Design the POM structure (POM Design)

**Workflow:** `/generate-automation-from-testcases`(continued)
**Skill:**`/qa-automation-engineer`

---

## Purpose

After the AI ​​has collected locators from the real DOM in Step 2, this step guides the AI ​​to shape the **Class Pages** according to POM standards. The goal is to allocate the correct file and function name with a clear meaning **before** installing the test logic.

## How to use

1. Make sure to review and confirm the locators from Step 2.
2. Send files`prompt.txt`for AI.
3. AI will outline:
   - Class name Page + file path
   - Locator declarations (from Step 2)
   - Method signatures + body
4. Review POM architecture → go to Step 4.

## Naming rules

| Ingredients | Rules | Example |
|---|---|---|
| **Class name** | PascalCase + suffix`Page` | `LoginPage`, `CustomerFormPage`|
| **Locator** | camelCase, element description |`emailInput`, `submitButton`|
| **Method** | camelCase, describes business actions |`fillLoginForm()`, `verifySuccessToast()` |
| **File** | Theo convention framework | `LoginPage.ts` / `LoginPage.java` |

## Note

- This step only designs **Page classes**, NOT generating test scripts.
- Method should return`this`or target Page (fluent pattern) to chain.
- Comply with internal rules`.agents/rules/automation_rules.md`.
