# Step 6: Standardize Format (Template Mapping)


---

## Purpose

Package all Test Cases generated in Step 5 into standard Markdown tables, ready to copy to **Excel**, **Google Sheets**, or import straight to **Jira/TestRail/Xray/Zephyr**.

## How to use

1. Send files`prompt.txt`after reviewing test cases in Step 5.
2. AI will export the Markdown table with the format:```
   | TC ID | Module | Risk Level | Test Title | Pre-Condition | Test Steps | Expected Result | Priority | Test Data |
   ```
3. Copy the table results → paste into the test management tool.

## TC ID rule

Default format:`[PROJECT]_[MODULE]_TC_[NUMBER]`

For example:`CRM_CUST_TC_001`, `CRM_LOGIN_TC_001`

If the project has its own ID convention, change it in section`[Custom]`of prompt.txt.

## Handle when it's too long

If the total number of Test Cases > 30, AI will divide itself into **Part 1, Part 2...** and ask you before continuing. Guarantee:
- Do not miss any TC between sections
- Continuous TC ID serial number between parts