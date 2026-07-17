---
name: generate-application-test-plan
description: Explore the web application, generate test plans and test scenarios. Supports 2 modes — PLAN (test plan only) and FULL (test plan + automation skeleton).
---

# Workflow: Explore Application & Generate Test Plan

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/qa-automation-engineer`** (in`.agents/skills/qa-automation-engineer/SKILL.md`) before starting. Additionally, refer to additional skills **`/requirements-analyzer`** and **`/ui-debug-agent`** to support UI analysis.

This workflow helps agents automatically explore a web application, analyze the structure, identify important modules/user flows, and generate a complete Test Plan.

## ⚠️ Implementation principles

- **All output in English**
- **DO NOT guess** the app structure — must inspect the actual DOM via MCP/browser tools
- **Must wait for user confirmation** scope in Step 2 before generating details
- If the user has not provided a URL → ask before starting

## 2 Modes

| Mode | When to use | Output |
|---|---|---|
| **PLAN** (default) | Users need to explore the app, create a test plan, and identify scenarios Modules, User Flows, Test Scenarios, Priorities |
| **FULL** | User requests additional automation skeleton or says "full automation suite" | Like PLAN + Manual Test Cases + Automation Skeleton (POM + Test classes) |

> If the user says "generate full automation suite", "bootstrap automation", or requests code → automatically switches to **Mode FULL**.

## Steps to perform

### Step 1: Acquire & Explore Applications (Recon)

1. Get application URL from user
2. Use **MCP browser tools** (Playwright MCP) to open the application:
   -`browser_navigate` → URL
   - `browser_resize(1920, 1080)` → desktop viewport
   - `browser_snapshot`→ collect DOM structure
3. Explore **navigation menus**, sidebar, header to identify main modules
4. Access each main module in turn and use it`browser_snapshot`to note:
   - Module/page name
   - Main UI components (forms, tables, buttons, modals)
   - Possible actions (CRUD, search, filter, export...)
5. If the app requires login → ask the user to provide credentials or use an existing fixture

### Step 2: Analyze & Confirm scope (Analysis — CHECKPOINT)

1. Summarize the findings into a list:
   - **Discovered Modules** (name, short description, number of features)
   - **Main User Flows** (Happy Path for each module)
   - **Dependencies** between modules (if any)
2. Assess the preliminary **Risk Level** for each module:
   - 🔴 **High Risk** — Core module, affects many users, complex logic
   - 🟡 **Medium Risk** — Auxiliary module, used frequently
   - 🟢 **Low Risk** — Module is rarely used, rarely changed
3. **⏸️ STOP — Present for user review:**
   - List of modules + risk level
   - User flows have been identified
   - Ask the user: "Which modules do you want to focus on? Are there any flows that need to be added?"
4. **Wait for the user to confirm** the scope before going to Step 3

### Step 3: Generate Test Scenarios & Priorities

1. For each module/flow that has been confirmed by the user, generate test scenarios:
   - **Happy Path** — main flow successful
   - **Negative Path** — incorrect input, missing data, validation error
   - **Edge Cases** — boundary values, concurrent access, empty states
2. Assign **Priority** to each scenario based on Risk Level:
   - **P1 (Critical)** — Core flows, regression blockers, sensitive data
   - **P2 (High)** — Main features, frequently used features
   - **P3 (Medium)** — Secondary features, UI/UX checks
   - **P4 (Low)** — Nice-to-have, cosmetic checks

### Step 4: Packaging Test Plan (Output — Mode PLAN)

1. Create **artifact**`test_plan.md`with structure:
   - **App overview** — purpose, tech stack (if identified), URL
   - **List of Modules** — table includes: Module, Description, Risk Level, Number of scenarios
   - **User Flows** — describes each main flow (steps)
- **Test Scenarios** — table:`| ID | Modules | Scenario | Priorities | Type (Happy/Negative/Edge) |`- **Automation Candidates** — check which scenarios should be automated and why
2. If the user selects **PLAN Mode** → **END** here

### Step 5: Generate Manual Test Cases (Mode FULL)

> Only execute when in **Mode FULL**

1. Convert test scenarios (Step 3) into **full manual test cases**:
   - TC ID, Module, Test Title, Pre-conditions, Test Steps, Expected Results, Test Data, Priority
2. Test Data must be **specific** (no generic placeholders)
3. Export as Markdown table in artifact

### Step 6: Generate Automation Skeleton (Mode FULL)

> Only execute when in **Mode FULL**

1. Determine **tech stack** automation (ask user if unclear):
   - Default: Playwright + TypeScript (or Selenium + Java according to preference)
2. Generate **Page Object classes** for each module:
   - Locator collected from actual DOM (Step 1), NO guessing
   - Clear interactive methods
3. Generate **Test class skeleton** for top-priority scenarios (P1, P2)
4. Ensure compliance with **POM pattern** and **locator strategy** according to rules

## Output

### PLAN mode
- Artifact`test_plan.md`includes: App overview, Modules, User Flows, Test Scenarios (with Priority), Automation Candidates

### Mode FULL
- All PLAN Mode outputs, plus:
- Manual Test Cases (Markdown table)
- Page Object classes
- Test class skeletons
- Assertions validating expected behavior