---
name: rbt-manual-testing
description: Skill to generate manual test cases with 2 modes — QUICK (quick generation from requirements) and FULL RBT (6-step AI-RBT process with risk assessment). Master skill for all manual test case tasks.
---

# RBT Manual Testing

## Description

This is the **Master Skill** for all tasks of creating manual test cases. Skill provides **2 operating modes** (modes) to suit all scale requirements:

| Mode | When to use | Time |
|-------|-------------|-----------|
| **QUICK** | Simple module, need fast implementation, clear requirements | 1 turn (no user waiting) |
| **FULL RBT** | Complex module, need to analyze risks, large system | 6 sequential steps (with checkpoint) |

**Core principles:**
- **Human Strategy:** Humans determine strategy, risk levels, and standards
- **AI Execution:** AI performs analysis, writes TCs and checks vulnerabilities
- **Human Verification:** Humans check the results before finalizing

---

## When to Use

Use this skill when:

- Generate manual test cases from requirements / user stories
- Analyze requirements to detect ambiguity
- Decompose the system into modules / features
- Build traceability matrix
- Apply Risk-Based Testing (risk assessment for test cases)
- Standardize test cases to Markdown table (Jira/Excel format)
- Generate test cases quickly from simple requirements

**DO NOT** use this skill when:

- Need to generate automation code → use`/qa-automation-engineer`
- Need to inspect DOM / generate locator → use `/ui-debug-agent` / `/smart-locator-agent`
- Just generate test data → use `/test-data-generator`

---

## Mode Routing — How to select the mode

Agent automatically selects mode based on **trigger keywords** and **context**:

### → Mode QUICK

Activates when:
- User uses workflow`/generate-testcases-from-requirements`
- User says: "generate test cases quickly", "create test cases from this requirement", "write test cases for the form..."
- Requirements are clear, scope is small (1 module / 1 feature)
- Users do not require risk analysis or formal processes

### → Mode FULL RBT

Activates when:
- User uses workflow`/generate-manual-testcases-rbt`
- User said: "6-step process", "RBT analysis", "generate complete test cases", "generate a methodical set of specifications"
- Large scope (many modules, complex system)
- User requests Traceability Matrix or Risk Level assessment
- Requirements are not clear, need to analyze Ambiguity

### → When unclear

If the mode cannot be determined, the agent **asks the user**:```
In what mode do you want to generate test cases?
1. QUICK — Quickly generate from requirements (without going through the analysis step)
2. FULL RBT — Full 6-step process (analysis → decomposition → RBT → TC generation)
```

---

# Mode 1: QUICK — Sinh Test Cases Nhanh

## Purpose

Generate **fast, high-quality test cases** from clear requirements/user stories, suitable for simple modules or when immediate results are needed.

## Process (1 turn only)

**Right Agent:**

1. **Read and understand the requirements** provided
2. **Identify main streams:**
   - Happy Path (main thread)
   - Negative Path (wrong, missing data)
   - Boundary Cases (boundary value)
3. **Apply automated test case design techniques**:
   - **Equivalence Partitioning (EP):** Divide input into equivalent groups
   - **Boundary Value Analysis (BVA):** Test value at the boundary
   - **Decision Table:** Lists combinations of conditions (if there are multiple rules)
   - **State Transition:** Test state transition (if there is a workflow)
4. **Field-Level Validation:**
   - List **all input fields** on form/UI
   - Generate validation test cases **separately for EACH field** according to its own characteristics
   - Apply validation checklist according to field type (see Field-Level Validation table below)
   - **DO NOT** combine validation of multiple fields into 1 test case
5. **Generate test cases** with complete fields:
- TC ID (format:`[PROJECT]_[MODULE]_TC_[NUMBER]`)
   - Modules
   - Test Case Title / Test Scenario
   - Pre-conditions
   - Test Steps (numbered)
   - Expected Results (numbered accordingly)
   - Test Data (**must be specific**, no placeholder)
   - Priority (Critical / High / Medium / Low)
6. **Export to standard Markdown table**, ready to copy to Excel/Jira

## Output table

```
| TC ID | Module | Test Scenario | Pre-Condition | Test Steps | Test Data | Expected Result | Priority |
```

## Test Data rules (applies to both modes)

```
❌ Incorrect: "Enter a valid code"
✅ Correct: "Enter code: KH-2026-0012"

❌ Incorrect: "Enter a valid email"
✅ Correct: "Enter email: test_khachhang_01@domain.com"

❌ Wrong: "Enter value beyond limit"
✅ Correct: "Enter 256 characters in the Name field (max: 255)"
```

## Field-Level Validation table (applies to both modes)

When the form/UI has input fields, the agent is **REQUIRED** to list each field and generate separate validation TCs by type:

| Field Type | Validation needs testing |
|---|---|
| **Text (Name, Address...)** | Required/Optional · Min length · Max length · Whitespace-only · Special characters (`<>&"'`) · XSS injection (`<script>alert(1)</script>`) · SQL injection (`' OR 1=1--`) · Unicode/Emoji · Leading/trailing spaces |
| **Email** | Valid format (`user@domain.com`) · Lack`@`· Missing domain · Invalid domain · Many`@`· Special characters first`@`· Max length · Case sensitivity · Email already exists (if unique) |
| **Phone** | Only accept valid · Prefix numbers (e.g.:`+84`, `0`) · Min/Max length · Mixed letters · Accents`-`, `.`, space · Invalid area code |
| **Date / DateTime** | Correct format (dd/MM/yyyy, ISO...) · Date does not exist (`31/02`, `30/02`) · Leap year (`29/02/2024`) · Past / future date (depending on business rules) · Min/max date value · Timezone (if applicable) |
| **Number / Currency** | Min/Max value · Negative numbers · Zeros · Decimals · Non-numeric characters · Overflow (extremely large numbers) · Leading zeros · Currency format (comma, period) |
| **Dropdown / Select** | Default value · All valid options · Option disabled · Change selection · Required validation (not selected) |
| **Checkbox / Radio** | Default state · Check/Uncheck · Required validation · Radio group (select only 1) |
| **File Upload** | Valid/invalid file type · Max size · Empty file (0 KB) · File name with special characters · Multiple files (if allowed) · Drag and drop vs select button |
| **Password** | Min/Max length · Requires special characters · Requires upper/lower case letters · Requires numbers · Copy-paste blocked? · Show/hide password · Confirm password match/mismatch |
| **Textarea** | Max length · Line breaks · HTML tags · Resize (if UI allows) · Character counter (if available) |

> **Principle:** Each field has its own characteristics → its own validation. Agent MUST analyze each field before generating TCs, cannot use the same validation set for all fields.

## Anti-Patterns (Mode QUICK)

- ❌ Generate generic test data / placeholder
- ❌ Only has Happy Path, lacks Negative/Boundary
- ❌ Ignore validation rules in requirements
- ❌ Test Steps are vague ("enter data" → must clearly state what and where to enter)
- ❌ Combine validation of multiple fields into 1 test case → each field must have its own validation TC
- ❌ Use the same validation set for all fields (each field type has its own checklist)
- ❌ Ignore security validation (XSS, SQL injection) for text fields

---

# Mode 2: FULL RBT — 6-Step AI-RBT Process

## Purpose

Methodical and sequential process for complex modules. Includes Ambiguity analysis, system decomposition, Traceability Matrix, Risk Level assessment, and detailed test case generation.

> ⚠️ **IMPORTANT:** This process **MUST be run sequentially** step by step. DO NOT combine multiple steps at once. Each step must be completed and confirmed by the user before moving on to the next step.

> [!NOTE]
> **2 separate usage streams:**
> - **Codex skill flow:** Follow the general instructions below. No need to read files`prompt.txt`.
> - **Copy-Paste flow:** QA team copies words sequentially`plans/manual/01_context_and_roleplay/prompt.txt`arrive`plans/manual/06_template_mapping/prompt.txt`Enter chat AI, step by step.

### Step 1: Context & Role-play (Initialize context)

**Purpose:** Set up the Senior QA Engineer role and load the project context.

**Right Agent:**
1. Require the user to provide:
   - Project/feature name
   - Describe the current system
   - MVP testing goals
   - Requirement documents (Requirements, User Stories, Figma links, PDF...)
2. Read the document carefully and confirm understanding of the context
3. Summary of testing scope
4. **Wait for user confirmation** before going to Step 2

**Output:** Confirm understanding of context + summarize test scope.

---

### Step 2: Analysis & QnA (Requirements Analysis)

**Purpose:** Analyze documents to detect blurriness, omissions, and contradictions.

**Right Agent:**
1. Identify streams:
   - Happy Path (main thread)
   - Alternate Paths (branch streams)
   - Exception Paths (exception streams)
2. Detect Ambiguities:
   - Missing requirements (not specifying textbox length, timeout, connection loss behavior...)
   - Conflicting requirements
   - Requirements are not clear
3. Ask numbered Q&A questions (Q1, Q2...) for the user/PO/BA to answer, each question with context and assumption if not answered
4. **STOP — Wait for the user to answer** the questions before continuing

**Output:** Stream List + Ambiguities + Q&A Questions.

> [!IMPORTANT]
> **This is the most important bottleneck.** If the agent skips this step and guesses the logic, the test case will be seriously wrong. The agent MUST stop and wait for the user to respond.

---

### Step 3: Decomposition (System decomposition)

**Purpose:** Divide complex features into small, easy-to-manage Modules / Sub-modules.

**Right Agent:**
1. Decompose in 1 of 2 ways:
   - **According to UI:** Header, Data Table, Form popup, Sidebar...
   - **By flow:** New creation flow, Edit flow, Delete flow...
2. Briefly describe the function of each Module
3. Indicate Dependencies between Modules

**Output:** List of Modules/Sub-modules + Dependencies.

---

### Step 4: Traceability (Ensure coverage)

**Purpose:** Set up a traceability matrix to ensure 100% of requirements are covered in test scenarios.

**Right Agent:**
1. Map each Module/Rule with the Request code (REQ-01, REQ-02...)
2. Cross-check to see if any requirements are missing from the breakdown list (Gap Analysis)
3. List High-Level Test Scenarios for each Module, focusing on:
   - Security / decentralization
   - UI Validation
   - Business Logic
   - Data Integrity
   - Error Handling
4. **Wait for user review** list of scenarios before creating detailed test cases

**Output:** Traceability Matrix + High-Level Test Scenarios.

> [!WARNING]
> **Human Checkpoint:** User needs to review the scenario list to add specific cases that AI may miss. This is the human risk assessment step.

---

### Step 5: RBT & TC Generation (Generate detailed Test Cases)

**Purpose:** Generate detailed test cases according to the Risk-Based Testing strategy.

**Right Agent:**
1. Assess Risk Level for each Module:
   - **High Risk:** Thorough testing, many cases (important operations, money related, security)
   - **Medium Risk:** Moderate test
   - **Low Risk:** Basic test, happy path
2. Generate test cases with complete fields:
   - Module / Sub-module
   - Test Case Title
   - Pre-conditions
   - Test Steps (numbered)
   - Expected Results (numbered accordingly)
   - Test Data (**must be specific**, do not use generic placeholders)
   - Priorities
3. Diverse coverage:
   - Happy Path
   - Negative Path (boundary value, exceeding characters)
   - Edge Cases (timeout, lost connection...)
4. **Field-Level Validation:**
   - List **all input fields** on the form/UI under test
   - Generate validation TCs **separately for EACH field** according to specific characteristics
   - Refer to the **Field-Level Validation Table** in the Mode QUICK section to select the appropriate validation
   - **DO NOT** combine validation of multiple fields into 1 TC
5. Apply appropriate **test case design techniques**:
   - **Equivalence Partitioning:** Divide input into equivalent groups, test a representative of each group
   - **Boundary Value Analysis (BVA):** Test value at boundary (min, min+1, max-1, max)
   - **Decision Table:** Lists the combination of conditions → results (for multiple-condition logic)
   - **State Transition:** Test valid + invalid state transition (for workflow)
6. If the scenarios are too many → generate each Module one by one, ask the user to continue

**Output:** List of detailed Test Cases with Risk Level.

---

### Step 6: Template Mapping (Format Standardization)

**Purpose:** Package test cases into standard Markdown tables, ready to copy to Excel/Jira.

**Right Agent:**
1. Standardize all test cases into Markdown table:

```
| TC ID | Module | Risk Level | Test Title | Pre-Condition | Test Steps | Expected Result | Priority | Test Data |
```

2. Table rules:
   - TC ID in a unified format (for example:`CRM_CUST_TC_001`)
   - Test Steps and Expected Result are numbered and used`<br>`line break in cell
   - **ABSOLUTELY do not omit** any test cases generated in Step 5
   - If it's too long → divide it into Part 1, Part 2... and ask the user to continue
3. Export output as Artifact (`test_cases_<module>.md`)

**Output:** Complete Test Cases Markdown table.

---

## Anti-Patterns (STRICTLY PROHIBITED — applies to both modes)

- ❌ Combine multiple steps at once in FULL RBT (MUST be sequential)
- ❌ Self-guess business logic without asking the user (Step 2 - FULL RBT)
- ❌ Skip the Ambiguity analysis step (FULL RBT)
- ❌ Generate generic test data / placeholder
- ❌ Shortening or omitting test cases when mapping to a table
- ❌ Generate all test cases at once for a large system (must be divided into modules)
- ❌ Only has Happy Path, lacks Negative/Boundary cases (QUICK)
- ❌ Test Steps are vague, do not specify input data
- ❌ Combine validation of multiple fields into 1 test case → each field must have its own validation TC
- ❌ Use the same validation set for all fields (Email ≠ Phone ≠ Date ≠ Text)
- ❌ Ignore security validation (XSS, SQL injection) for text/textarea fields
- ❌ Do not list fields before generating validation TCs

---

## Prompt Templates

Sample prompt templates for the FULL RBT process are located at:

```
plans/manual/
├── 01_context_and_roleplay/prompt.txt
├── 02_analysis_and_qna/prompt.txt
├── 03_decomposition/prompt.txt
├── 04_traceability/prompt.txt
├── 05_rbt_and_tc_generation/prompt.txt
└── 06_template_mapping/prompt.txt
```

The agent needs to read the corresponding prompt template **before** executing each step (FULL RBT mode).

QUICK mode does not require reading prompt templates — the agent applies EP/BVA/Decision Table techniques directly.

---

## Output Format

### Mode QUICK

| Output | Description |
|--------|--------|
| TC Markdown table | Full Test Cases, ready to copy to Excel/Jira |

### Mode FULL RBT

| Step | Output |
|--------|--------|
| 1 | Confirm context |
| 2 | Stream + Ambiguities + Q&A |
| 3 | Module Decomposition + Dependencies |
| 4 | Traceability Matrix + High-Level Scenarios |
| 5 | Detailed Test Cases (Risk Level + Test Data) |
| 6 | Standard Markdown table (Jira/Excel ready) |

All output must be in **Vietnamese**, **Markdown** format, use **Artifact** if the content is long.