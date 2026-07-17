# Step 1: Initialize context (Context & Role-play)

**Workflow:** `/generate-automation-from-testcases`
**Skill:** `/qa-automation-engineer`

---

## Purpose

Configure the AI ​​to the role of **Senior Automation Engineer** and load the technical context. This step helps AI know exactly:
- Which framework (Playwright / Selenium / Appium)
- Which language (TypeScript / Java)
- What is the project architecture like?
- Code principles must be followed

## How to use

1. Open the file`prompt.txt`.
2. Replace internal parts`[...]`:
   - **Tech Stack:** Choose framework, language, build tool
   - **Goal:** System/feature needs automation
   - **Context:** Web architecture, frontend technology, element specifics
   - **Architecture:** Copy from`project_architecture/README.md`or describe an existing project
3. Send to AI and wait for confirmation → go to Step 2.

## Note

- Choose the right framework from the beginning — AI will generate code that follows that framework syntax throughout.
- If the project already exists, describe the existing structure so that AI will generate code in the correct directory.
- This step only needs to be run **once** at the beginning of the conversation.