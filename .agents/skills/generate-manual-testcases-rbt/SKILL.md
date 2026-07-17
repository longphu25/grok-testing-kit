---
name: generate-manual-testcases-rbt
description: Generate high-quality manual test cases according to the 6-step AI-RBT (Risk-Based Testing) process from requirements.
---

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/rbt-manual-testing`** (in`.agents/skills/rbt-manual-testing/SKILL.md`) before starting this task. Use **Mode FULL RBT** of skill. Additionally, refer to additional skills **`/requirements-analyzer`** to understand how to parse the interface if needed.

# Workflow: Sinh Manual Test Cases theo AI-RBT Framework (FULL RBT Mode)

This workflow uses the **Mode FULL RBT** of the skill`/rbt-manual-testing`— **AI-RBT (AI-Driven Risk-Based Testing)** process includes 6 sequential steps to generate manual test cases from requirements documents.

> [!NOTE]
> **This thread is a Codex skill.** Follow the instructions in the skill, no need to read the file`prompt.txt`.
> If the QA team wants to use a more detailed prompt, copy-paste the words sequentially`plans/manual/01_context_and_roleplay/prompt.txt`arrive`plans/manual/06_template_mapping/prompt.txt`.

## ⚠️ Implementation principles

- **Mode:** FULL RBT (6 sequential steps)
- **REQUIRED to run sequentially** each step, DO NOT combine multiple steps
- **MUST stop** waiting for user feedback at Step 2 (Q&A) and Step 4 (Review Scenarios)
- If the user has not provided requirements, ask the user to provide them before starting
- All output in **Vietnamese**

## Steps to perform

Follow the detailed instructions in the skill`/rbt-manual-testing`→ section **Mode 2: FULL RBT**.

### Step 1: Initialize context (Context & Role-play)
1. Ask the user to provide: project name, system description, MVP goals, requirements documents
2. Read the document carefully, confirming understanding of the context
3. **Wait for user confirmation** → go to Step 2

### Step 2: Requirements Analysis (Analysis & QnA)
1. Identify Happy Paths, Alternate Paths, Exception Paths
2. Detect Ambiguities (omissions, contradictions, unclearness)
3. Ask numbered Q&A questions (Q1, Q2...) to user/PO/BA, with context + assumption
4. **STOP — Wait for the user to answer the question** → go to Step 3

### Step 3: System decomposition (Decomposition)
1. Divide features into Modules / Sub-modules
2. Describe the functions of each Module + Dependencies between them

### Step 4: Ensure coverage (Traceability)
1. Map Module → Request code (REQ-01, REQ-02...)
2. Cross-check gaps (Gap Analysis), listing High-Level Scenarios
3. **Wait for user review** scenarios → go to Step 5

### Step 5: Generate detailed Test Cases (RBT & TC Generation)
1. Assess Risk Level (High/Medium/Low) for each Module
2. Generate complete test cases: Title, Pre-condition, Steps, Expected, Test Data, Priority
3. Apply techniques: EP, BVA, Decision Table, State Transition
4. **Field-Level Validation:**
   - List all input fields on the form/UI under test
   - Generate validation TCs **separately for EACH field** according to specific characteristics
   - Refer to **Field-Level Validation Table** in skill`/rbt-manual-testing`
- **DO NOT** combine validation of multiple fields into 1 TC
5. Full coverage: Happy Path, Negative, Boundary, Edge Cases
6. Test Data must be specific (no general placeholder)
7. If there are too many → generate each Module, ask the user to continue

### Step 6: Standardize the Format (Template Mapping)
1. Pack all test cases into a standard Markdown table:`| TC ID | Module | Risk Level | Test Title | Pre-Condition | Test Steps | Expected Result | Priority | Test Data |`
2. Do not miss any test cases
3. Export as Artifact if long

## Output

- Complete Markdown Test Cases table, ready to copy to Excel/Jira/TestRail
- Traceability Matrix
- Ambiguities list resolved