---
name: generate-combinatorial-test-data
description: Generate test data for multidimensional association matrices using a real pipeline running through many modules. Use the output from the skill generate-cross-module-test-plan as input.
---

# /generate-combinatorial-test-data — Generate Test Data for Combination Matrix

> **Use when:** There is an association matrix (from`/generate-cross-module-test-plan`) and need to **create actual test data** by running through multiple modules on the browser, or generate structured data sets ready for automation.

> **MANDATORY:** Before starting, MUST load and read carefully:
> - **Skill:**`.agents/skills/test-data-generator/SKILL.md`— Data generation rules (see Multi-Step Pipeline section)
> - **Skill:**`.agents/skills/ui-debug-agent/SKILL.md`— Inspect DOM when running the browser
> - **Workflow:**`.agents/skills/generate-cross-module-test-plan/SKILL.md`— Understand the input matrix structure

---

## Relationships with other workflows```
/generate-cross-module-test-plan → Combination matrix (input)
        ↓
/generate-combinatorial-test-data → Test data set (this workflow)
        ↓
/generate-manual-testcases-rbt → Detailed test cases
        ↓
/generate-automation-from-testcases  →  Automation scripts
```---

## 2 Modes

| Mode | When to use | Output |
|-------|-------------|--------|
| **GENERATE** (default) | Generate structured data sets from matrices — DO NOT run browser | JSON/CSV/Markdown file with data for each combination |
| **PIPELINE** | Need to create REAL data on the system by running through each module on the browser | Real data created + IDs + screenshots evidence |

> Agent chooses mode:
> - User says "generate data", "generate data" → **GENERATE**
> - User says "create data on the system", "run to create data", "set up real data" → **PIPELINE**
> - If not clear → ask user

---

## Input needed from User

| Input | Required | Description |
|-------|----------|-------|
| **Combination Matrix** | ✅ | File`.md`/word Markdown table`/generate-cross-module-test-plan`|
| **Application URL** | ✅ (PIPELINE) | Let the agent run the browser to create data |
| **Credentials** | ⚠️ PIPELINE | If the app needs to log in |
| **Output format** | ❌ |`json`(default),`csv`, `markdown`, `code`(TS/Java/Python) |
| **Data language** | ❌ | Vietnamese / English (default: according to context) |

---

## Steps to perform

### Step 1: Read & Parse the Combination Matrix

1. **Read matrix file** from user provided:
   - File local →`view_file`- Inline in chat → parse directly
   - URL →`read_url_content`2. **Parse and validate:**
   - Define a list of dimensions (D1, D2, D3...)
   - Determine the values of each dimension
   - Read expected template/formula for each set
   - Count the total number of combinations that need to generate data

3. **Summary presentation:**```markdown
📊 Matrix read:
   - Dimensions: 5 (Partners, Payments, Taxes, Debt, Asset Source)
   - Total combinations: 20 (Pairwise)
   - Mode: GENERATE / PIPELINE
   
Start generating data? (Y/N)
   ```---

### Step 2: Analyze Fields & Data Requirements Each Module

1. **For each module** in the series, determine the fields that need data:```markdown
   | Module | Field | Type | Required | Constraints | Data Source |
   |--------|-------|------|----------|-------------|-------------|
| Partners | partner_name | string | ✅ | max: 200 | Random + prefix |
   | Partners | partner_type | select | ✅ | enum: [TC, CN, HKD] | From dimension D1 |
   | Partners | tax_id | string | ✅ | 10-13 digits | Random unique |
   | Payment | currency | select | ✅ | enum: [VND, USD] | From dimension D2 |
   | Payment | amount | number | ✅ | min: 1 | Business-relevant values ​​|
   | Tax | tax_type | select | ✅ | enum: [PIT, VAT, NT, MT] | From dimension D3 |
   | ...| ... | ... | ... | ... | ... |
   ```2. **Classification of fields:**

   | Type | Description | How to generate data |
   |-------|-------|----------------|
   | **Dimension fields** | Dimension value in matrix | Retrieved from combinator (not random) |
   | **Supporting fields** | Fields are required but not dimensions | Generate random + unique + traceable |
   | **Computed fields** | Self-adjective formula | Calculated according to business rules |
   | **Reference fields** | ID/code from previous module | Copy from previous output module |

---

### Step 3: Generate Test Data — Mode GENERATE

> Execute when mode = GENERATE (default)

1. **For each set of combinations** in the matrix, generate a complete set of data:```json
   {
     "combination_id": "COMBO_01",
     "dimensions": {
       "D1_partner_type": "Tổ chức",
       "D2_payment_type": "VND",
       "D3_tax_type": "VAT 10%",
       "D4_debt_type": "Thông thường",
       "D5_asset_source": "Quỹ A"
     },
     "module_data": {
       "module_1_partner": {
         "partner_name": "auto_combo01_tc_1712049200",
         "partner_type": "Tổ chức",
         "tax_id": "0123456789",
         "address": "Số 1 Nguyễn Huệ, Q1, HCM"
       },
       "module_2_payment": {
         "currency": "VND",
         "amount": 100000000,
         "payment_date": "2026-04-15",
         "description": "Thanh toán combo01"
       },
       "module_3_tax": {
         "tax_type": "VAT",
         "tax_rate": 10,
         "tax_amount": 10000000
       },
       "module_4_debt": {
         "debt_type": "Thông thường",
         "advance_amount": 0
       }
     },
     "expected_output": {
       "template": "BB_TC_VND_VAT",
       "formula": "Amount × 1.10",
       "computed_total": 110000000,
       "expected_fields": ["partner_name", "tax_id", "amount", "tax_amount", "total"]
     }
   }
   ```2. **Generate enough data for ALL combinations** → pack into 1 output file

3. **Ensure data rules:**
   - Unique per combo (no overlap between sets)
   - Traceable: prefix`auto_combo{XX}_{dimension_short}`- No real PII
   - Computed values must match the formula

---

### Step 3P: Generate Test Data — PIPELINE Mode (run in real browser)

> Execute when mode = PIPELINE

1. **Open browser with MCP:**```
browser_navigate → Application URL
   browser_resize → 1920 × 1080
   ```2. **Loop through each combination:**```
   FOR each combo in matrix:
     FOR each module in chain:
       1. Navigate → module URL
2. browser_snapshot → confirm state
       3. Fill in data according to combo sets:
          - Dimension fields → select value according to combo
          - Supporting fields → sinh random + traceable
       4. Submit / Save
       5. browser_wait_for → confirm success
6. browser_snapshot → capture results
       7. Extract output (ID, code...) → save for the next module
     END FOR
     
// In the last module — verify output
     8. Capture final minutes/output
     9. browser_take_screenshot → evidence
10. Record: combo_id, created_ids, template_found, formula_verified
   END FOR
   ```3. **Error handling in pipeline:**

   | Error | How to handle |
   |-----|-----------|
   | Submit failed (validation) | Screenshot → log → skip combo → notify user |
   | Module loads slowly |`browser_wait_for`with increasing timeout |
   | Session expired | Re-login → retry from failed module |
   | Duplicate data | Regenerate unique data, retry |
   | Invalid combo (constraint) | Skip → mark "INVALID" in report |

4. **Pipeline limitations:**
   - Maximum **30 combinations**/session (avoid timeout)
   - If > 30 → split batch, ask user "Continue batching?"
   - After every 10 sets → notify the user of progress

---

### Step 4: Output Packaging & Reporting

#### Output for GENERATE Mode:

Create artifact file(s) according to user requested format:

**JSON (default):**```json
{
  "feature": "Biên bản thanh toán đối tác",
  "generated_at": "2026-04-15T17:00:00Z",
  "strategy": "pairwise",
  "total_combinations": 20,
  "dimensions": ["partner_type", "payment_type", "tax_type", "debt_type", "asset_source"],
  "data_sets": [
    { "combination_id": "COMBO_01", "dimensions": {...}, "module_data": {...}, "expected_output": {...} },
    { "combination_id": "COMBO_02", ... }
  ]
}
```

**Markdown Table:**
```markdown
| Combos | Partners | Payment | Tax | Debt | Source | Partner Name | Amount | Expected Templates | Expected Total |
|-------|---------|-----------|------|---------|-------|-------------|--------|------------------|----------------|
| 01 | Organization | VND | VAT | Usually | Fund A | auto_c01_tc | 100M | BB_TC_VND_VAT | 110M |
| 02 | ... | ... | ... | ... | ... | ... | ... | ... | ... |
```

**Code (TypeScript example):**
```typescript
// test-data/payment-record.data.ts
export const combinatorialData = [
  {
    id: 'COMBO_01',
    partner: { name: `auto_combo01_${Date.now()}`, type: 'Tổ chức', taxId: '0123456789' },
    payment: { currency: 'VND', amount: 100_000_000 },
    tax: { type: 'VAT', rate: 10 },
    expected: { template: 'BB_TC_VND_VAT', total: 110_000_000 },
  },
  // ... more combos
];
```

#### Output cho Mode PIPELINE:

```markdown
## Pipeline Execution Report

| # | Combo | Status | Module 1 ID | Module 2 ID | Module 3 ID | Output Template | Formula ✓ | Screenshot |
|---|-------|--------|------------|------------|------------|-----------------|-----------|------------|
| 1 | COMBO_01 | ✅ PASS | PTR-001 | PAY-001 | TAX-001 | BB_TC_VND_VAT | ✅ Match | combo01.png |
| 2 | COMBO_02 | ✅ PASS | PTR-002 | PAY-002 | TAX-002 | BB_TC_USD_PIT | ✅ Match | combo02.png |
| 3 | COMBO_03 | ❌ FAIL | PTR-003 | PAY-003 | — | — | — | combo03_fail.png |

### Summary
- ✅ Passed: 18/20
- ❌ Failed: 2/20 (COMBO_03: Tax module validation error, COMBO_17: Timeout)
- 📊 Data created: 18 partners, 18 payments, 18 tax configs, 18 minutes
```---

## Data Rules (REQUIRED)

| # | Rule | Description |
|---|-------|-------|
| 1 | **Unique per combo** | Each combo uses its own data — not shared between combos |
| 2 | **Traceable** | Prefix:`auto_combo{XX}_{dimension_short}_{timestamp}`|
| 3 | **Dimension values ​​exact** | The dimension value MUST be exactly as in the matrix — NOT random |
| 4 | **Supporting random fields** | Fields do not belong to dimension → generate random + unique |
| 5 | **Computed values ​​verified** | The calculated value must match the formula in the matrix |
| 6 | **No real PII** | DO NOT use real personal data |
| 7 | **Include expected output** | Each combo MUST have expected template + formula + computed values ​​|

---

## STRICTLY PROHIBITED

| ❌ Do not do | ✅ Correct replacement |
|-------------------|-----------------|
| Random dimension values ​​| Dimension values ​​are taken exactly from the matrix |
| Hardcode data is the same for all combos | Data unique per combo with traceable prefix |
| Ignore expected output | Each combo MUST have expected template + values ​​|
| Run pipeline > 30 combos/session no questions asked | Divide into batches of 30, ask user to continue |
| Ignore failed combos, do not report | Full log: which combo failed, why, screenshot |
| Read`.env`to get credentials | Ask User or use existing fixture |

---

## Final checklist

- [ ] Read and parsed the full combination matrix
- [ ] Classify fields: dimension / supporting / computed / reference
- [ ] Generated data unique per combo + traceable
- [ ] Dimension values are 100% correct compared to the matrix
- [ ] Computed values are correct according to formula
- [ ] (PIPELINE) Screenshots evidence for each combo
- [ ] (PIPELINE) Report pass/fail for each combo
- [ ] Does not contain real PII
- [ ] Output file in the correct format required by the user