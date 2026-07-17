---
name: fetch-jira-requirements
description: Get Requirements / User Stories from Jira ticket and save as markdown or JSON file.
---

# Workflow: Fetch Jira Requirements

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/jira-integration`** (in`.agents/skills/jira-integration/SKILL.md`) to learn how to use the scripts before starting.

This workflow helps take requirements, user stories, or issues from Jira and convert them into documents for test automation.

## Steps:

1. **Check prerequisites:**
   - Read skills`/jira-integration`to grasp the structure of scripts.
   - Check files`.env`already exists and is configured correctly.
   - Check installed dependencies (`scripts/integrations/node_modules/`).
   - If not installed, run:`cd scripts/integrations && npm install`2. **Determine the information to get:**
   - Ask the user to provide information:
     - Specific **Issue key** (eg:`PROJ-123`) → use`--issue`
     - **Project key + Issue type** (VD: `PROJ`, `Story`) → use`--project --type`- **JQL query** custom → use`--jql`- **Epic key** (get children) → use`--epic`- Determine output format:`json`(default) or`md`(markdown requirements)

3. **Execute script:**
   - Run the appropriate command:```bash
   # Lấy 1 issue cụ thể
   node scripts/integrations/jira/jira_fetcher.js --issue <ISSUE_KEY>

   # Lấy issues theo project
   node scripts/integrations/jira/jira_fetcher.js --project <PROJECT_KEY> --type <TYPE> --max <N>

   # Tìm theo JQL
   node scripts/integrations/jira/jira_fetcher.js --jql "<JQL_QUERY>"

   # Xuất Markdown
   node scripts/integrations/jira/jira_fetcher.js --issue <KEY> --format md --output ./requirements/jira
   ```4. **Result processing:**
   - Check the output file created in`requirements/jira/`(or`--output`assign).
   - If format`json`: Read and display issues summary for users.
   - If format`md`: Displays markdown requirement content for user review.
   - If you encounter an error: Read the log, analyze the cause according to the Troubleshooting table in the skill.

5. **Handling common errors:**
   - **HTTP 401**: Wrong token → instruct user to check`JIRA_API_TOKEN`or`JIRA_PAT`- **HTTP 404**: Issue does not exist or`JIRA_BASE_URL`wrong
   - **File .env not found**: Instructions for users to copy`.env.example` → `.env`- **Module not found**: Run`npm install` trong `scripts/integrations/`6. **Delivery:**
   - Present results in English.
   - If the user's request is converted into a requirement document, use skill`/requirements-analyzer`to reformat.
   - Save the output file to the appropriate folder (`requirements/jira/`).
