---
name: requirements-analyzer
description: Skills to analyze websites/modules and create standard Requirements Documents/User Stories.
---

# Requirements Analysis Skills (Requirements Analyzer)

This skill guides Codex to convert the UI or DOM/HTML structure of a website into clear, detailed requirements documents for QA, Testers and Developers.

## 1. Core goal
- Develop requirements documents that closely follow the actual running system.
- Ensure consistency and coverage for both Happy Path and Edge Cases (Exceptions/error reports).
- Professional output format (Using Artifact structure).

## 2. Information extraction process
When asked to create Requirements from a website:
1. **Layout Analysis:** Identify the Header, Footer, Sidebar, and Main Content sections.
2. **Collect Form & Inputs:**
   - Find all input fields (`input`, `select`, `textarea`).
   - Attribute recognition`type` (text, email, password, number), `required`, `maxlength`, `minlength`, `pattern`.
3. **Collect Interactive Buttons (Buttons/Links/Actions):**
   - Determine the function of each button (Save, Submit, Cancel, Delete, Edit).
   - Warnings and notifications (Alerts, Toasts, Validation Messages) appear when interacting with errors.
4. **Extract Workflows:**
   - Dependencies between components (eg: Submit button is only enabled when the "I agree" Checkbox is checked).

## 3. Output Requirements Document Structure (Output Format)
The document needs to be formatted according to professional Markdown or saved as Artifact (`requirements_spec.md`).

**Must have content:**

### 3.1. Overview (Overview)
Brief description of the website/module's features and purpose.

### 3.2. Functional Requirements
Divide into **User Stories** or **Use Cases**:
- **Feature name** (Example: Login Function)
- **Description:** "As a user, I want... to be able to..."
- **Acceptance Criteria:** Specify the conditions that need to be satisfied.

### 3.3. Field Specifications
Here is the core part for Automation Tester:
* Use Markdown table (*Markdown Table*) to list:
  - School Name (Label)
  - Type (Type UI)
  - Validation Rules (Required / Default / Length limit).
  - Notes.

### 3.4. Business Rules & Validations (Business Rules & Validations)
List details of expected Validation Messages when users enter incorrect data.

## 4. Strict Rules
- Always write in **Vietnamese**.
- Do not deduce complex business requirements without evidence from the UI. If logic is missing, list them under "Questions/Clarifications with PO-User".
- If you have Playwright MCP, prioritize opening a real browser to screenshot/capture the interface if necessary.