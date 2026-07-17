---
name: jira-integration
description: Jira/Xray integration skill — get requirements from Jira, validate Xray, and push test results to Xray Cloud/Server.
---

# Jira & Xray Integration Skill

## Describe

This skill provides integration between Codex workspace and Jira and Xray systems to:

1. **Get Requirements/User Stories** from Jira → convert into standard requirements document
2. **Xray Authentication** (Cloud or Server/Data Center)
3. **Push test results** (Playwright, JUnit, Allure) to Xray Test Management

---

## When to use

Agent uses this skill when the user requests:

- Get requirements / user stories from Jira
- Connect/test connection to Jira API
- Push test results to Xray
- Import test results to Jira
- Xray token authentication
- Integrate CI/CD with Jira

Trigger keywords:
- "fetch jira", "get requirements from jira", "get jira ticket"
- "import xray", "push results to xray", "push test results"
- "test jira connection", "test jira connection"

---

## Scripts structure

```
scripts/integrations/
├── jira/
│ ├── jira_fetcher.js # Get Requirement/User Story from Jira
│ ├── xray_auth.js # Authenticate and get Xray Token
│ ├── xray_importer.js # Import test results to Xray
│ └── utils.js # Shared utility function
└── package.json             # Dependencies (axios, dotenv)
```

---

## Prerequisites (Prerequisites)

### 1. Install dependencies

```bash
cd scripts/integrations
npm install
```

### 2. Configure .env

Copy `.env.example`wall`.env`in the project root directory:

```bash
cp .env.example .env
```

Fill in the required information:

| Variable | Description | Required |
|-------|--------|----------|
|`JIRA_BASE_URL` | URL Jira instance (VD: `https://domain.atlassian.net`) | ✅ |
| `JIRA_EMAIL`| Jira account email (Cloud) | ✅ (Cloud) |
|`JIRA_API_TOKEN` | API Token (Cloud) | ✅ (Cloud) |
| `JIRA_PAT` | Personal Access Token (Server/DC) | ✅ (Server) |
| `JIRA_PROJECT_KEY`| Default project key | Recommendations |
|`XRAY_PLATFORM` | `cloud`or`server`| Default: cloud |
|`XRAY_CLIENT_ID`| Xray API Client ID | When using Xray Cloud |
|`XRAY_CLIENT_SECRET`| Xray API Client Secret | When using Xray Cloud |

### 3. How to get Jira API Token (Cloud)

1. Log in to [https://id.atlassian.com/manage-profile/security/api-tokens](https://id.atlassian.com/manage-profile/security/api-tokens)
2. Click **Create API token**
3. Set label (eg "Codex Automation")
4. Copy token → paste`JIRA_API_TOKEN` trong file `.env`

### 4. How to get Xray Cloud API Key

1. Go to [https://app.getxray.app](https://app.getxray.app) → Settings → API Keys
2. Or in Jira: Apps → Xray → Settings → API Keys
3. Create new API Key → Copy **Client ID** and **Client Secret**

---

## Instructions for use

### Get a specific issue

```bash
node scripts/integrations/jira/jira_fetcher.js --issue PROJ-123
```

### Get issues by project

```bash
node scripts/integrations/jira/jira_fetcher.js --project PROJ --type Story --max 20
```

### Search by JQL

```bash
node scripts/integrations/jira/jira_fetcher.js --jql "project = PROJ AND status = 'To Do'"
```

### Export to Markdown requirements

```bash
node scripts/integrations/jira/jira_fetcher.js --issue PROJ-123 --format md
```

### Get Epic's children

```bash
node scripts/integrations/jira/jira_fetcher.js --epic PROJ-10 --format md
```

### Test Xray authentication

```bash
node scripts/integrations/jira/xray_auth.js
node scripts/integrations/jira/xray_auth.js --verify
```

### Import Playwright results to Xray

```bash
node scripts/integrations/jira/xray_importer.js --format playwright --file ./test-results.json --project PROJ
```

### Import JUnit XML to Xray

```bash
node scripts/integrations/jira/xray_importer.js --format junit --file ./junit-results.xml --project PROJ
```

---

## Related Workflows

| Workflow | Description |
|----------|--------|
|`/fetch-jira-requirements`| Get requirements from Jira ticket and save as file |
|`/import-test-results-xray`| Push test results to Xray |

---

## Important note

- **Security**: Never commit files`.env`to Git. File`.gitignore`has been configured to ignore`.env`.
- **Rate Limiting**: Jira Cloud has a limit on API calls. The script supports pagination to avoid exceeding the limit.
- **Atlassian Document Format (ADF)**: Jira Cloud uses ADF for description. Script automatically converts ADF → plain text.
- **Test Key Convention**: When importing Playwright results, you should put the test key in the title:`test('[PROJ-123] Login should work', ...)`for Xray mapping to correct test cases.

---

## Troubleshooting

| Error | Cause | Solution |
|-------|-------------|-----------|
| HTTP 401 | Wrong token/password | Check JIRA_API_TOKEN or JIRA_PAT |
| HTTP 403 | No permissions | Check permissions on Jira project |
| HTTP 404 | Wrong URL or issue does not exist | Check JIRA_BASE_URL and issue key |
|`ENOTFOUND`| DNS does not resolve | Check if JIRA_BASE_URL has the correct domain |
|`ECONNREFUSED`| Server is not running | Check if Jira Server is online |
| File .env not found | No .env | created yet Copy`.env.example` → `.env` |
