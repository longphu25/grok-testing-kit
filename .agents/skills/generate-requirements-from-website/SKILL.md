---
name: generate-requirements-from-website
description: Generate Requirements content from a provided website module
---

# Workflow: Generate Requirements from Website Module

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/requirements-analyzer`** (in`.agents/skills/requirements-analyzer/SKILL.md`) for standard Requirements document format before starting this task.

This workflow helps you analyze a provided module or website and generate detailed, accurate Requirements documents for testing or development.

## Steps:

1. **Information Gathering:**
   - Carefully read the instructions from the skill **`/requirements-analyzer`** to capture learning outcomes.
   - Get website URL information, module name, or description/image provided by the user.
   - If necessary, ask users about login information or special statuses that need attention.

2. **System Survey (Recon & Investigation):**
   - Use web browsing tools (Browser tools/MCP) or`read_url_content`to access the requested website module.
   - Carefully inspect the HTML structure, DOM, input forms, interactive buttons (buttons, links), and error messages (validation messages).
   - *Note: Do not guess information fields yourself if you cannot see them on the actual interface.*

3. **Analyze UI & Interactions:**
   - Analyze user flows.
   - Record static and dynamic data fields (For example: TextBox, Dropdown, Checkbox).
   - Record business rules displayed in the interface (Business Rules) such as: mandatory fields (mandatory fields), valid formats (email, phone number), or character limits.

4. **Compiling Draft Requirements documents:**
   - Based on the data collected, create a descriptive document that includes:
     * **Overview:** Purpose of the module/page.
     * **Functional Requirements:** List of features the user can perform (eg: Login, Add new, Delete...).
     * **Field Specifications:** Detailed table of each UI element (Field name, Type, Required/No, Data binding).
     * **Business/User Flows:** Steps to complete a main function.
     * **Non-functional Requirements (If observable):** Compatibility, static performance.

5. **Presentation and Delivery (Review & Delivery):**
   - Format documents using Markdown clearly.
   - Present all content in **Vietnamese** with clear, professional and easy-to-understand accents.
   - Use the Artifact feature if the document is long for users to conveniently store or export files.