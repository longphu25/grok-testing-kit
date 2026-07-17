# Codex Prompt Templates

This directory contains sample prompts that have built-in **Codex skill** calls.

When pasting the prompt into Codex App, CLI or IDE Extension, Codex will recognize the skill in the first line and read the file`SKILL.md`respectively in`.agents/skills/`and follow the correct workflow.

---

## Prompt List

| # | File | Codex workflow skills | Foundation skills |
|---|-------|---------------------|----------------|
| 01 |`prompt_01_generate_requirements.txt` | `/generate-requirements-from-website` | `/requirements-analyzer` |
| 02 | `prompt_02_generate_test_cases.txt` | `/generate-testcases-from-requirements` | `/rbt-manual-testing` |
| 03 | `prompt_03_create_framework_playwright.txt` | `/generate-automation-framework` | `/framework-architect` |
| 03 | `prompt_03_create_framework_selenium.txt` | `/generate-automation-framework` | `/framework-architect` |
| 04 | `prompt_04_generate_script_playwright.txt` | `/generate-automation-from-testcases` | `/qa-automation-engineer` |
| 04 | `prompt_04_generate_script_selenium.txt` | `/generate-automation-from-testcases` | `/qa-automation-engineer` |
| 05 | `prompt_05_convert_manual_to_automation.txt` | `/generate-automation-from-testcases` | `/qa-automation-engineer` |
| 07 | `prompt_07_generate_test_data.txt` | `/generate-test-data` | `/test-data-generator` |
| 08 | `prompt_08_analyze_flaky_tests.txt` | `/analyze-flaky-tests` | `/flaky-test-analyzer` |
| 09 | `prompt_09_generate_api_tests.txt` | `/generate-api-tests-from-swagger` | `/qa-automation-engineer` |

> Prompt 02 uses **QUICK mode** by default`/generate-testcases-from-requirements`. To run the full 6-step AI-RBT process, change the first line to`/generate-manual-testcases-rbt`.

## How to Use

1. Select the appropriate prompt file.
2. Replace the placeholders`[...]`with real data.
3. Keep the line intact`/skill-name`at the beginning of the file.
4. Copy the entire content and paste it into Codex.
5. Check checkpoints or clarification questions required by the skill.

## Name Convention

- Codex skills are called by accents`$`.
- Skill name uses **kebab-case**, for example`/generate-automation-from-testcases`.
- Physics skills are located at`.agents/skills/<skill-name>/SKILL.md`.
- Do not use the old slash command`/generate_automation_from_testcases`.
