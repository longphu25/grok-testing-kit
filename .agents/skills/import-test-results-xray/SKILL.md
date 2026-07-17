---
name: import-test-results-xray
description: Push automation test results (Playwright/JUnit/Allure) to Xray Test Management.
---

# Workflow: Import Test Results to Xray

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/jira-integration`** (in`.agents/skills/jira-integration/SKILL.md`) to learn how to use the scripts before starting.

This workflow helps push automation test results to Xray (Cloud or Server/Data Center) for tracking on Jira.

## Steps:

1. **Check prerequisites:**
   - Read skills`/jira-integration`to grasp the structure of scripts.
   - Check files`.env`Configured Xray credentials:
     - **Xray Cloud**:`XRAY_CLIENT_ID`, `XRAY_CLIENT_SECRET`
     - **Xray Server**: `JIRA_PAT`or`JIRA_EMAIL` + `JIRA_API_TOKEN`- Check`XRAY_PLATFORM` (cloud/server) trong `.env`.
   - If dependencies have not been installed:`cd scripts/integrations && npm install`

2. **Verify Xray authentication:**
   ```bash
   node scripts/integrations/jira/xray_auth.js --verify
   ```- If failed: check credentials in`.env`3. **Determine the report to import:**
   - Ask the user what type of report:
     - **Playwright JSON**:`--format playwright --file <path-to-results.json>`
     - **JUnit XML**: `--format junit --file <path-to-junit.xml>`- **Xray JSON** (already converted):`--format xray --file <path-to-xray.json>`- Confirm **Project Key** (from`.env`or`--project`)

4. **Implement import:**```bash
   # Playwright report
   node scripts/integrations/jira/xray_importer.js --format playwright --file ./test-results.json --project PROJ

   # JUnit XML
   node scripts/integrations/jira/xray_importer.js --format junit --file ./junit-results.xml --project PROJ

   # Xray JSON
   node scripts/integrations/jira/xray_importer.js --format xray --file ./xray-payload.json
   ```5. **Result processing:**
   - Check output: Test Execution Key created on Jira.
   - If successful: notify the user key of the new Test Execution.
   - If failure: read log → analyze cause → fix → run again.

6. **Important note:**
   - **Test Key Convention**: Test title should contain Jira key for automatic Xray mapping:```typescript
     test('[PROJ-123] Login should work', async ({ page }) => { ... });
     ```- **Xray Cloud** needs its own authenticate (Client ID + Secret), different from Jira auth.
   - **Rate limit**: Avoid importing too many times in a row.