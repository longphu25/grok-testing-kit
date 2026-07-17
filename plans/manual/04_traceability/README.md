# Step 4: Ensure coverage (Traceability & Gap Analysis)


---

## Purpose

Cross-check and set up **Traceability Matrix** to ensure 100% of original requirements are covered by Test Scenarios.

## How to use

1. Send files`prompt.txt`After reviewing the decomposition results in Step 3.
2. AI will return:
   - **Traceability Matrix:** Map table REQ ID ↔ Module ↔ Scenario
   - **Gap Analysis:** Report gaps (if any)
   - **High-Level Scenarios:** List of high-level scenarios
3. **Review carefully** list of scenarios:
   - Are there any scenarios missing?
   - Are there any redundant/duplicate scenarios?
   - Add more if necessary → Confirm → go to Step 5.

## ⚠️ Human Checkpoint

**This is a personnel blocking point.** Reason:

- AI may miss project-specific edge cases.
- Testers (humans) need to self-assess the **level of risk** for each module **before** allowing AI to generate detailed test cases in Step 5.
- If a deficiency is detected, ask AI to add: *"Please add scenarios for case [X]"*.