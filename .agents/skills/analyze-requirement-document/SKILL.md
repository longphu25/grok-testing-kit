---
name: analyze-requirement-document
description: Analyze requirement document (Jira ticket, .doc, user story) — generate detailed analysis documents, NOT test cases.
---

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/requirements-analyzer`** (in`.agents/skills/requirements-analyzer/SKILL.md`) to understand how to analyze standard requirements before starting.

# Workflow: Requirement Document Analysis

This workflow analyzes requirement documents (Jira tickets, .doc files, user stories, design mockups) and generates a detailed analysis document. **DO NOT create test cases** — just focus on understanding, decomposing, and detecting risks/ambiguities in the requirements.

## When to use

- User provides Jira ticket (.doc) or requirement document and requests "analysis"
- Users want to clearly understand scope, acceptance criteria, and dependencies before writing tests
- User needs a list of ambiguities to clarify with PO/BA
- User says: "analyze requirements", "review requirements", "analyze this ticket"

## Input

Agent needs to collect from user:

| # | Input | Required | Description |
|---|---|---|---|
| 1 | **Requirement document** | ✅ | .doc, .md file, Jira URL, or request description text |
| 2 | **Mockup/Screenshot** | ⭕ Recommended | Current UI design image, wireframe, or screenshot |
| 3 | **Related tickets** | ⭕ Options | Dependencies or related tickets (dependencies) |
| 4 | **Additional Context** | ⭕ Options | Information about the current system, business domain |

> [!NOTE]
> If the user only provides a .doc file without a mockup, the agent must still fully analyze it based on the document content. If there is a mockup/screenshot, the agent analyzes the UI in more detail.

## Steps to perform

### Step 1: Collect and understand (Information Gathering)

1. **Read the requirement document** provided by the user (.doc, .md file, or URL)
   - If .doc file is HTML format (export from Jira): parse HTML to extract content
   - Identify: Ticket ID, Type, Priority, Status, Reporter, Assignee, Fix Version, Sprint, Labels
2. **Read mockup/screenshot** if available — analyze UI layout, components, fields
3. **Check related tickets** if available in the same folder or provided by the user
- Read and summarize dependencies
4. **Confirm** you have grasped the context → continue analysis

### Step 2: Extract core information (Core Analysis)

1. **Ticket Overview** — Metadata table (ID, Type, Priority, Status, Sprint, Assignee...)
2. **User Story** — Extract format "As a... I want... So that..."
3. **Scope of application (Scope)** — Clearly identify affected modules/pages/components
4. **Acceptance Criteria** — Decompose each AC into logical groups, including:
   - Detailed description of each AC
   - Comparison table (if there is a new column, new field, new rule)
   - Clearly distinguish **default vs optional** (if applicable)

### Step 3: Analyze UI from Mockup (if any)

If user provides mockup/screenshot:

1. **Layout description** — Breadcrumb, header, sidebar, main content, footer
2. **List components** — Tables, forms, modals, buttons, dropdowns, tabs
3. **Fields details** — Field name, type (input/dropdown/date picker), label, placeholder
4. **Compare** mockup with document — detect inconsistency
5. **Screenshot** on carousel in artifact (if image available)

### Step 4: Analyze Dependencies (Dependencies)

1. Identify related tickets/features (referenced in AC or comments)
2. Read and summarize the dependent ticket content
3. If there is a separate mockup for dependencies → detailed UI analysis (fields, modals, interactions)
4. Synthesize **Business Rules** from all requirements + mockups
5. Clearly mark which rule is from the main ticket vs the dependent ticket

### Step 5: Detect Ambiguities & Risks (Focus)

> [!IMPORTANT]
> This is the **most valuable** part of the workflow — discovering what the requirement does NOT explicitly say.

**5.1. Ambiguities:**

For each ambiguity, state:
- **Code:** AMB-XX (sequential numbering)
- **Question:** Clearly describe what is unclear
- **Risk:** Impact if not resolved
- **Level:** 🔴 High / 🟡 Medium / 🟢 Low

Directions for detecting ambiguity:
- Ambiguous keywords: "where applicable", "as needed", "similar to", "etc."
- Validation rules missing: min/max, format, required/optional
- Edge case behavior: network error, concurrent access, empty data
- Inconsistency between document and mockup (column names, format, layout)
- Unknown Threshold/config (e.g. how many days = "approaching deadline"?)
- Conflict between old and new requirements

**5.2. Testing Risks:**

For each risk, clearly state:
- **Code:** RISK-XX
- **Risk name**
- **Description**
- **Mitigation** (how to minimize)

### Step 6: Synthesis & Delivery (Synthesis & Delivery)

1. **State Matrix** (if there are state transitions) — state → behavior mapping table
2. **Checklist AC** — Summary of all ACs in checkbox format, grouped by function
3. **Testing recommendations** — Suggestions for the top 10 things to pay attention to most when testing
4. **Export Artifact** — Save the entire analysis to a file`.md`## Output Structure (Template Artifact)

The agent MUST export the artifact according to the following structure:```markdown
# 📋 Requirement Analysis: [TICKET-ID]
## [Ticket Title]

## 1. Ticket Overview
(Metadata table)

## 2. User Story
(As a... I want... So that...)

## 3. Scope of Application (Scope)
(List of affected modules/pages)

## 4. Acceptance Criteria — Detailed Analysis
### 4.1. [AC Group 1]
### 4.2. [AC Group 2]
### 4.N. [AC N Group]

## 5. Dependencies
### 5.1. [Dependent tickets]
#### 5.1.1. [UI details if mockup available]
#### 5.1.N. General Business Rules

## 6. Mockup/Screenshot Analysis
### 6.1. [Mockup 1]
### 6.N. [Mockup N]

## 7. Ambiguities & Risks
### 7.1. Ambiguities
(Table: #, Question, Risk, Level)
### 7.2. Testing Risks
(Table: #, Risk, Description, Mitigation)

## 8. State Matrix (if applicable)
(State table → behavior)

## 9. Acceptance Criteria Summary (Checklist)
(Checkbox grouped by function)

## 10. Recommendations for Testing
(Suggested list, NOT test cases)
```## Important rules

- ❌ **DOES NOT generate test cases** — this workflow only analyzes, does not create test cases
- ❌ **DO NOT guess** business logic if the document does not clearly state it → include Ambiguities
- ❌ **DO NOT ignore comments** in Jira tickets — comments often contain additional important information
- ✅ **MUST read related tickets** if referenced in AC
- ✅ **MUST analyze mockup** in detail if provided (fields, layout, interactions)
- ✅ **MUST clearly state inconsistency** between document and mockup
- ✅ **MUST write in English**, Markdown format, export Artifact
- ✅ **MUST copy the image** to the artifacts folder if you need to embed it in the artifact

## Relationships with other workflows

| After completing the analysis | Next Workflow |
|---|---|
| Need to generate test cases quickly |`/generate-testcases-from-requirements`|
| Need to create test cases methodically (6-step RBT) |`/generate-manual-testcases-rbt`|
| Need to generate automation scripts |`/generate-automation-from-testcases`|
| Need cross-module analysis |`/generate-cross-module-test-plan` |
