# Step 2: Analyze & UI Recon (Analysis & UI Recon)

**Workflow:** `/generate-automation-from-testcases`(continued)
**Skill:**`/qa-automation-engineer` + `/ui-debug-agent`

---

## Purpose

Instead of humans having to manually Inspect the DOM, this step tasks the AI ​​with opening the browser, navigating the web, and extracting real locators from the DOM. This is the step that differentiates the quality between "guess" automation and "real inspection" automation.

## How to use

1. Fill in the URL, test account and Test Cases in the file`prompt.txt`.
2. Send to AI — AI will use Playwright/Selenium MCP to:
   - Automatically start the browser
   - Navigate by test steps
   - Collect locators from real DOM
3. Review the locators table returned by AI.
4. If any locator is not correct → ask AI to re-inspect that element.
5. Confirm → go to Step 3.

## Important note

- AI will use **Accessibility Tree** and **DOM inspection** to find the locator — no guessing.
- If you need to log in, provide a test account in the prompt.
- Default viewport **1920x1080** (desktop) according to the rules in the rules.
- AI prioritizes locators in order`.agents/rules/locator_strategy.md`.
