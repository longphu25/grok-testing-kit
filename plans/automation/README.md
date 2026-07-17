# AI-DRIVEN AUTOMATION TESTING FRAMEWORK

**Goal:** 
Use AI to automate the process of building Automation Framework, designing POMs and generating high-quality code — easy to maintain, CI/CD friendly.

## 📌 Core Principles

1. **AI DOM Recon First:** AI must use browser MCP (Playwright/Selenium) to open the browser and inspect the real DOM. ABSOLUTELY do not guess the locator.
2. **POM Architecture:** All scripts comply with the Page Object Model — clearly separating Pages and Tests.
3. **Smart Waits & Stability:** No hard sleep (`Thread.sleep`, `waitForTimeout`). Use auto-waiting only.
4. **Deterministic Data:** Test data unique + traceable, no hardcode.
5. **Self-fix Loop:** AI runs the test itself → if it fails → reads the log, fixes the code, runs again → until PASS.

---

## 🚀 6 Step Process + Step 0 (Setup)

| Step | Name | Purpose | Skills |
|-------|-----|----------|-------|
| **0** | Project Architecture | Setup standard directory architecture |`/qa-automation-engineer`|
| **1** | Context & Role-play | Set up roles + tech stack |`/qa-automation-engineer`|
| **2** | Analysis & UI Recon | AI automatically opens the browser, collects locators |`/qa-automation-engineer` + `/ui-debug-agent`|
| **3** | POM Design | Design the Page Objects class |`/qa-automation-engineer` |
| **4** | Test Data Strategy | Sinh class Data Generator | `/qa-automation-engineer` + `/test-data-generator`|
| **5** | Script Generation | Generate test script + run it yourself + fix it yourself |`/qa-automation-engineer` |
| **6** | Review & Refactoring | Clean code + CI/CD readiness | `/qa-automation-engineer` |

*(Each step corresponds to 1 subfolder, including`README.md` + `prompt.txt`)*

---

## 🎯 Implementation Strategy

### Method 1: Sequential (Manual Control)

Use each file manually`prompt.txt`(01 → 06):
- **Suitable when:** Only needs AI to do one specific step (eg: "Only generate POM, no need to test script")
- **Or when:** Large project, want to meticulously control each module

### Method 2: One-Click Auto Workflow (Recommended)

Call workflow`/generate-automation-from-testcases`+ attached Test Cases:
- Agent order: Read TC → Open Browser → Get Locator → Generate POM → Generate Script → Run Test → Fix Bug → Until PASS
- **Advantages:** Completely automated after a single command

---

## 📋 Reference rules

- `.agents/rules/automation_rules.md` — POM, Data, Naming conventions
- `.agents/rules/locator_strategy.md`— Locator priority order
-`.agents/rules/playwright_rules.md` — Playwright-specific rules
- `.agents/rules/selenium_rules.md` — Selenium-specific rules
- `.agents/rules/appium_rules.md` — Appium-specific rules

## 📁 Quick guide

Xem file `QUICK_START.md`in this folder to get started quickly.