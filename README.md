# Grok Testing Kit

QA **skills + rules + plans + prompt templates** for **Grok** (xAI Grok CLI/TUI): Manual and Automation testing workflows.

**Remote:** `git@github.com:longphu25/grok-testing-kit.git`  
**HTTPS:** https://github.com/longphu25/grok-testing-kit

---

## References

This kit **adapts** (MIT) ideas and workflows from three open testing kits by [Anh Tester](https://anhtester.com):

| Repository | Original host | What we used |
| --- | --- | --- |
| [anhtester/claude-testing-kit](https://github.com/anhtester/claude-testing-kit) | Claude Code | Commands/rules layout under `.claude/` |
| [anhtester/antigravity-testing-kit](https://github.com/anhtester/antigravity-testing-kit) | Antigravity / Gemini-style | `.agent/skills`, global rules |
| [anhtester/codex-testing-kit](https://github.com/anhtester/codex-testing-kit) | OpenAI Codex | Primary skill set (26 workflows), `AGENTS.md`, `$skill` → `/skill` |

**Credit:** Anh Tester Automation Testing — https://anhtester.com · Vietnam testing community.  
**License:** MIT (see `LICENSE`).

This is **not** a 1:1 fork. Grok-specific changes:

- Invoke skills with slash commands: `/skill-name` (not `$skill-name`)
- `AGENTS.md` maps tools to the Grok tool surface
- Optional install as a **git submodule** under a host monorepo’s skill root

---

## Layout

```text
grok-testing-kit/
├── AGENTS.md
├── LICENSE
├── README.md
├── .agents/
│   ├── skills/               # 26 QA skills (each has SKILL.md)
│   └── rules/                # automation, locator, playwright, selenium, appium, delivery
├── plans/                    # manual · automation · cross-module
└── prompt_templates/         # one-shot prompts
```

---

## Standalone use

```bash
git clone git@github.com:longphu25/grok-testing-kit.git
cd grok-testing-kit
# Open Grok in this directory so it discovers .agents/skills/
```

Or point Grok config at the skills tree:

```toml
# ~/.grok/config.toml
[skills]
paths = ["~/src/grok-testing-kit/.agents/skills"]
```

Verify with `grok inspect`, then try e.g. `/generate-testcases-from-requirements`.

---

## Host monorepo (submodule)

Typical path when embedded in another project:

```text
.agent/skills/grok-testing-kit   →  this repository
```

Nested skills:

```text
.agent/skills/grok-testing-kit/.agents/skills/<skill-name>/SKILL.md
```

Host setup (example):

```bash
git submodule update --init --recursive .agent/skills/grok-testing-kit
```

Do **not** replace an entire multi-source skill directory with a single submodule if the host also installs other skill packs.

---

## Suggested workflows

**Manual**

```text
/generate-requirements-from-website
→ /generate-manual-testcases-rbt
→ /generate-test-data
```

**Automation**

```text
/generate-automation-framework
→ /generate-automation-from-testcases
→ /analyze-flaky-tests
```

**API**

```text
/generate-api-tests-from-swagger
```

---

## Developing this kit

```bash
git clone git@github.com:longphu25/grok-testing-kit.git
cd grok-testing-kit
# edit skills…
git add -A && git commit -m "docs: …" && git push origin main
```

On a host monorepo that vendors this pack as a submodule, update the gitlink after pull:

```bash
cd .agent/skills/grok-testing-kit && git pull origin main && cd -
git add .agent/skills/grok-testing-kit
git commit -m "chore(skills): bump grok-testing-kit submodule"
```

---

## Links

- Grok skills user guide (local install): `~/.grok/docs/user-guide/08-skills.md`
- Upstream kits listed in the References table above
