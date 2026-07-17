# Step 6: Review & Refactoring

**Workflow:** `/generate-automation-from-testcases`(continued)
**Skill:**`/qa-automation-engineer`

---

## Purpose

After testing, run PASS, "clean up" the code to meet **Clean Code** standards before merging into the repository. Avoid junk code that dirty the project.

## How to use

1. Send files`prompt.txt`After testing, PASS in Step 5.
2. AI will perform:
   - Delete debug logs, commented code, unused variables
   - Review code quality
   - Attach CI/CD tags (smoke, regression...)
3. Receive final code → commit/merge.

## Checklist Review

| # | Check | Status |
|---|---|---|
| 1 | No more`console.log` / `System.out.println`| ☐ |
| 2 | No more commented code | ☐ |
| 3 | There are no unused variables/locators | ☐ |
| 4 | No hard sleep | ☐ |
| 5 | Complete and accurate Assertions | ☐ |
| 6 | Test data unique, traceable | ☐ |
| 7 | Each test case is independent | ☐ |
| 8 | CI/CD tags attached | ☐ |

## Note

- This step can be run automatically in the One-Click workflow (AI automatically cleans up after PASS test).
- If running manually, paste the source code for AI review.
- Code must meet standards`.agents/rules/automation_rules.md`before committing.