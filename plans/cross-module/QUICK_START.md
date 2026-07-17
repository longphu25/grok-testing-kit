# 📋 Quick Guide: Cross-Module Testing & Combination Matrix

## 🔀 Select Usage Stream

### Flow 1: Antigravity (Slash Command) — Automatic (Recommended ⭐)

> Used when you are using **Antigravity** (Google Gemini plugin).

```
/generate-cross-module-test-plan

Feature: [Feature name, example: "Payment minutes to partners"]
URL: [https://your-app.com]
Account: [admin@test.com / Test@123]

Related modules:
1. [Module 1: Partner management — select partner type]
2. [Module 2: Create payment — select VND/USD]
3. [Module 3: Tax configuration — select tax type]
4. [Module 4: Debt management — choose debt type]
5. [Last module: Generate minutes — output]

Matrix strategy: pairwise (or: business-critical / full-cartesian)
```

→ AI automatically opens the browser → explores each module → draws Data Flow → generates combination matrix.

---

### Flow 2: Copy-Paste to ChatGPT / Claude — Manually

> Use when you want to use another AI (not Antigravity).

**Sample prompt — Copy and paste into AI chat:**

```
You are a Senior QA Engineer specializing in Combinatorial Testing.

I have a feature "[FEATURE NAME]" that goes through multiple modules one after another.
Each module has multiple options, and the combination of options determines the final output.

Modules and dimensions:
- Module 1 [NAME]: dimension [DIMENSION NAME] = [value 1, value 2, ...]
- Module 2 [NAME]: dimension [DIMENSION NAME] = [value 1, value 2, ...]
- Module 3 [NAME]: dimension [DIMENSION NAME] = [value 1, value 2, ...]
- ...

Let's do it:
1. Draw Data Flow Diagram between modules (which module outputs what → which module inputs)
2. List all constraints (invalid combinations)
3. Generate combination matrix table according to Pairwise Testing strategy
4. For each combination set, clearly state the Expected Output (template, formula if any)

Output in Markdown table format, ready to copy to Excel.
```

---

## 🎯 End-to-End Flow — Step by Step

### Step 1: Cross-Module Analysis & Matrix Generation

```
/generate-cross-module-test-plan

Feature: Minutes of payment to partners
URL: https://example.com/partners
```

**Result:** AI generated:
- 📊 Data Flow Diagram (which module → which module)
- 📋 Dimensions table (all dimensions + values)
- 📈 **Combination Matrix** (Pairwise ~20 sets instead of 216 Full sets)

**⏸️ User review:** Check matrix → add/edit → confirm OK.

---

### Step 2: Generate Test Data for Matrix

```
/generate-combinatorial-test-data

Matrix: [paste the matrix table from Step 1]
Mode: GENERATE (or PIPELINE if you want to create real data on the system)
Format: json (or: csv, markdown, typescript)
```

**Result:**
- GENERATE mode: JSON/CSV file contains N sets of data, each set = 1 combo
- PIPELINE mode: AI runs real browser → creates data on the system → reports pass/fail

---

### Step 3: Generate Test Cases (Optional)

```
/generate-manual-testcases-rbt

Requirements: [paste requirements + combination matrix from Step 1]
```

→ AI generates detailed test cases for each important combination.

---

### Step 4: Generate Automation Scripts (Optional)

```
/generate-automation-from-testcases

URL: https://example.com
Test cases: [paste test cases from Step 3]
Framework: Playwright TypeScript
```

→ AI generates scripts + runs automatically + fixes automatically → PASS stable.

---

## 📊 Practical Example: Partner Payment Minutes

### Input (you provide):

```
Feature: Minutes of payment to partners
Modules:
1. Partner Management: Type = [Organization, Individual, Business Household]
2. Payment: Type = [VND, USD]
3. Tax: Type = [PIT, VAT, Contractor, Tax Exempt]
4. Debt: Type = [Ordinary, Advance, Adjustment]
5. Source of assets: Type = [Fund A, Fund B, Fund C]
```

### Output (AI sinh ra):

**Data Flow:**
```
Partners → Payment → Tax → Debt → Minutes
(type) (currency) (Dependency (Dependency
                         type+currency) all)
```

**Pairwise matrix (20 sets instead of 3×2×4×3×3 = 216 sets):**

| # | Partners | TT | Tax | Debt | Source | Expected |
|---|--------|-------|--------|--------|-------|----------|
| 1 | Organization | VND | VAT | Usually | Fund A | BB_TC_VND_VAT |
| 2 | Organization | USD | PIT | Advance | Fund B | BB_TC_USD_PIT |
| 3 | Personal | VND | PIT | Usually | Fund A | BB_CN_VND_PIT |
| 4 | Personal | USD | VAT | Adjust | Fund C | BB_CN_USD_VAT |
| 5 | Business households | VND | Contractor | Usually | Fund B | BB_HKD_VND_NT |
| ... | ... | ... | ... | ... | ... | ... |

→ Cover 100% of pairs between any 2 dimensions, only need ~20 sets!

---

## 💡 Ultimate Tips

1. **Start with Pairwise** — good enough for 90% of cases, 80-90% less effort
2. **Provide business rules** if any — AI will map the "Expected Output" column more accurately
3. **Review Data Flow in Step 2** — this is the most important checkpoint, wrong here → the matrix will be wrong
4. **Use PIPELINE Mode** when testing data, you must actually create it via UI (cannot seed database)
5. **Run in the same conversation** with Antigravity so that AI keeps context throughout
6. **Batching** if matrix > 30 sets — avoid timeout and maintain quality

---

## ⚠️ Distinguish: When is this Workflow NOT needed?

| Situation | Which workflow to use? |
|-----------|-------------------|
| Test a single module, simple form |`/generate-manual-testcases-rbt`or`/generate-testcases-from-requirements`|
| Many modules but **independent** (not affecting each other) |`/generate-application-test-plan`|
| Multiple modules **in series**, output depends on **combinator** | ✅`/generate-cross-module-test-plan`← **Use this workflow** |
| Just need to test data for 1 form |`/generate-test-data` |
