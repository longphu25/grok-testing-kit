# Grok Testing Kit — workspace guidance

QA skill pack for **Grok** (TUI/CLI). Workflows adapted from Anh Tester’s Codex Testing Kit, cross-checked against Claude and Antigravity kits.

## Skills

- Skills live at `.agents/skills/<name>/SKILL.md`.
- Grok discovers `.agents/skills/` (and nested packs when this repo is mounted under a host skill root).
- Invoke with slash: `/skill-name` (example: `/generate-locator`). The model may also auto-invoke from `description`.
- On name collisions, use a qualified form if the host supports it (`/user:…`, `/local:…`).

## Rules (read per task)

Before writing or reviewing automation, load the matching file under `.agents/rules/`:

| File | When |
| --- | --- |
| `automation_rules.md` | All automation / test data |
| `locator_strategy.md` | Choosing locators |
| `playwright_rules.md` | Playwright |
| `selenium_rules.md` | Selenium |
| `appium_rules.md` | Appium |
| `delivery_checklist.md` | Before handoff |

## Tool map (legacy names → Grok)

| Intent in older skill text | Use in Grok |
| --- | --- |
| Read a file | `read_file` / shell |
| Edit a file | `search_replace` / write |
| Run tests / shell | `run_terminal_command` |
| Fetch web docs | `web_fetch` / `web_search` |
| Browser / DOM | installed browser skill (e.g. `agent-browser`) if available — do not invent tools |

## Working expectations

- Prefer clear, concise English in reports unless the user asks for another language.
- Do not run destructive git (`push`, force-push, hard reset, rewrite published history) unless the user explicitly asks.
- Locators: semantic + smart waits; avoid fixed sleeps unless required.
- Test data: unique, traceable, no real PII.
- After generating automation: run the narrowest relevant test/lint/compile command in the target project.

## Plans and prompts

- Multi-step flows: `plans/manual/`, `plans/automation/`, `plans/cross-module/` (see each `QUICK_START.md`).
- One-shot prompts: `prompt_templates/` (invoke lines use `/skill-name`).
