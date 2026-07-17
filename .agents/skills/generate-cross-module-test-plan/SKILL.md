---
name: generate-cross-module-test-plan
description: Analyze features through many serial modules, build Module Map + Dimension Catalog, and generate combination matrices (Pairwise/Business-critical/Full Cartesian). Supports 2 modes — DOCUMENT (from the document) and BROWSER (inspect the actual DOM).
---

# /generate-cross-module-test-plan — Cross-Module Analysis & Combination Matrix Generation

> **Used when:** The feature to be tested goes through **many modules in succession**, each module has many options (dimensions), and the combination of options determines the final output.

> **MANDATORY:** Before starting, MUST load and read carefully:
> - **Skill:**`.agents/skills/qa-automation-engineer/SKILL.md` — Workflow routing + automation rules
> - **Skill:** `.agents/skills/requirements-analyzer/SKILL.md`— Analyze requirements

---

## When to use this workflow?

| Situation | Use? |
|-------------|-------|
| Features go through **1 module/form** | ❌ Use`/generate-manual-testcases-rbt`|
| The feature comes across **multiple modules**, each **independent** | ⚠️ Use`/generate-application-test-plan`|
| The feature goes through **many SERIAL modules**, output depends on **condition combination** | ✅ **This exact workflow** |
| Need **combination matrix** (Multidimensional Pairwise / Decision Table) | ✅ **This exact workflow** |

---

## 2 Modes

| Mode | When to use | Main Input |
|-------|-------------|-------------|
| **DOCUMENT** (default) | User provides documentation/spec describing modules + business rules | File`.md`, `.doc`, Jira ticket, or text description |
| **BROWSER** | The user provides the URL and wants the agent to inspect the actual DOM | Application URL + credentials (if needed) |

> Agent chooses mode:
> - User provides file/text description → **DOCUMENT**
> - User provides URL or says "inspect", "open app", "view in browser" → **BROWSER**
> - User provides both → Priority **BROWSER**, use document for cross-reference
> - If not clear → ask user

---

## Input needed from User

| Input | Required | Description |
|-------|----------|-------|
| **Feature name / stream** | ✅ | For example: "Minute of payment to partners" |
| **Required documents** (DOCUMENT mode) | ✅ | File`.md`, Jira ticket, user story, or text description |
| **Application URL** (BROWSER mode) | ✅ | Let the agent inspect the actual DOM |
| **List of participating modules** | ⚠️ Should have | If not → agent automatically extracts from document/browser |
| **List of dimensions** | ⚠️ Should have | For example: partner type, tax type... If not → agent automatically extract |
| **Business rules / formula** | ❌ Optional | If yes → agent map into matrix |
| **Credentials** (BROWSER mode) | ❌ | If the app needs to log in |
| **Matrix Strategy** | ❌ |`pairwise`(default),`business-critical`, or`full-cartesian`|

---

## Steps to perform

### Step 1: Module Recon — Explore each Module

#### Mode DOCUMENT:

1. **Read the documentation** provided by the user (local file →`view_file`, URL → `read_url_content`, inline → direct parse)
2. **Extract module list** from the documentation:
   - Find sections that describe each step/module in the flow
   - Determine module order (which module comes first, which module comes next)
   - Identify the fields/controls mentioned for each module
3. **If the document is unclear** → ask the user for additional specific information

#### Mode BROWSER:

1. **Get a list of modules** from the user or explore yourself via navigation
2. **For each module** in the series:```
   browser_navigate → URL module
   browser_resize → 1920 × 1080
   browser_wait_for → page load
browser_snapshot → DOM capture
   ```3. **Collect for each module:**
   - Module name (from page title/breadcrumb)
   - Fields / Controls (input, select, radio...)
   - Selectable values (open dropdown → read options)
   - Validation rules (required, format, min/max)

#### Output Step 1 — Module Inventory (both modes):```markdown
| # | Modules | URL/Path | Main Inputs | Key Dimensions | Output | → Next Module |
|---|--------|-----------|-------------|---------------|--------|---------------|
| 1 | Partner Management | /partners | Name, Code, Type | **Partner type** (3 values) | Partner ID | → Payment |
| 2 | Create Payment | /payments/new | Amount, Type | **TT type** (2 values) | Payment ID | → Tax |
| ...| ... | ... | ... | ... | ... | ... |
```---

### Step 2: Data Flow & Dimension Extraction

> Combine Data Flow Mapping + Dimension Extraction into 1 step to reduce context switching.

1. **Define Data Flow** between modules:
   - What is Module A's **output**?
   - How does that output become **input/condition** of module B?

2. **Record Dependencies Matrix:**```markdown
| Target module | Depends on | Dependent field | Dependency Type |
   |-------------|--------------|-----------------|----------------|
| Payment | Partners | partner.type | Filter payment options |
   | Tax | Partners + Payments | Partner.type, Payment.currency | Decide on tax type |
   ```3. **Extract Dimensions** — variables that determine output:```markdown
| # | Dimension | Power module | Possible values ​​| Number values ​​|
   |---|------------------|-------------|----------------|-----------|
| D1 | Partner type | Partners | Organizations, Individuals, Business Households | 3 |
   | D2 | Payment Type | Payment | VND, USD | 2 |
   | D3 | Tax type | Tax | PIT, VAT, Contractor, Tax Exemption | 4 |
   ```4. **Calculate the total potential combination:**```
Full Cartesian: D1 × D2 × D3 × ... = total set of combinations
   ```5. **Define constraints** (invalid combination set):```markdown
| Constraint | Description | The set was eliminated |
   |-----------|-------|-----------|
| C1 | Individual + USD → no Contractor | 1 set |
   ```6. **Present to the user** the analysis results:
   - Module Inventory
   - Dependencies Matrix
   - Dimension Catalog
   - Constraints
   - **Continue automatically** to Step 3 (DO NOT stop the checkpoint — only stop if the user actively says something wrong)

---

### Step 3: Generate the Combination Matrix (CORE OUTPUT ⭐)

Agent supports **3 strategies** — user chooses or agent recommends:

#### 3A. Pairwise Testing (Default — RECOMMENDED)

> Make sure every **pair of 2 values** from any 2 dimensions is tested at least once.
> Significantly reduce the number of combinations while still covering 100% of pairs.

**How to do it — MUST use script, NOT manual calculation:**

1. Agent **generates Python script** using the library`allpairspy`:

   ```python
   # pairwise_generator.py
   from allpairspy import AllPairs
   
   dimensions = {
       "D1_partner_type": ["Tổ chức", "Cá nhân", "Hộ KD"],
       "D2_payment_type": ["VND", "USD"],
       "D3_tax_type": ["PIT", "VAT", "Nhà thầu", "Miễn thuế"],
       # ... thêm dimensions
   }
   
   # Constraints (bộ không hợp lệ)
   def is_valid(row):
       # Ví dụ: Cá nhân + USD → không có Nhà thầu
       if len(row) >= 3:
           if row[0] == "Cá nhân" and row[1] == "USD" and row[2] == "Nhà thầu":
               return False
       return True
   
   values = list(dimensions.values())
   keys = list(dimensions.keys())
   
   print(f"| # | {' | '.join(keys)} |")
   print(f"|{'---|' * (len(keys) + 1)}")
   for i, combo in enumerate(AllPairs(values, filter_func=is_valid)):
       row = ' | '.join(str(v) for v in combo)
       print(f"| {i+1} | {row} |")
   ```2. Agent **runs script** → reads output → formats into Markdown table
3. If`allpairspy`not installed →`pip install allpairspy`before running

> **STRICTLY PROHIBITED** agent calculates pairwise manually — LLM does not guarantee mathematical correctness.

#### 3B. Business-Critical Only

> Select only the **most important** combinations according to business risk.

**Selection criteria:**
- The most popular combination in practice (according to user confirmation)
- Ministry related to money, taxes, financial decisions → High Risk
- Edge combiner (edge cases between types)

**Quantity:** Usually 8-15 sets. Agent proposes → user confirms.

#### 3C. Full Cartesian (Full)

> Test ALL valid combinations. Used when the total number is ≤ 50 or the user requests it.

#### Output Step 3 — Matrix Table:```markdown
## Combination Matrix (Pairwise — N sets)

| # | D1: Partners | D2: Payment | D3: Tax | D4: Debt | Risk |
|---|------------|---------------|---------|------------|------|
| 1 | Organization | VND | VAT 10% | Usually | High |
| 2 | Organization | USD | PIT 10% | Advance | High |
| 3 | Personal | VND | PIT 10% | Usually | High |
| ... | ... | ... | ... | ... | ... |
```> **Notes on Expected Template / Formula:**
> - If the user provides business rules → add "Expected Output" column to the table
> - If NO → **DO NOT add this column**, let the next workflow process
> - DO NOT fill out`[Need user confirmation]`— if you don't know, leave it blank, don't create noise

---

### Step 4: Packaging Output & Generating Data (CHECKPOINT ⏸️)

> This is the **ONLY checkpoint** — the agent stops here waiting for the user to confirm.

1. **Main artifact generation:**

   **File:`cross_module_test_plan_<feature>.md`**
   - Inventory module (Step 1)
   - Dependencies Matrix (Step 2)
   - Dimension Catalog + Constraints (Step 2)
   - **Combination Matrix** (Step 3) — THIS IS THE MAIN OUTPUT
   - Risk Assessment for each combination set

2. **(Optional) Generate test data at the same time** if the user requests`--with-data`:

   For each set of combinations in the matrix, generate a set of data:```json
   {
     "combination_id": "COMBO_01",
     "dimensions": { "D1": "Tổ chức", "D2": "VND", "D3": "VAT 10%" },
     "supporting_data": {
       "partner_name": "auto_c01_tc_<timestamp>",
       "tax_id": "0123456789",
       "amount": 100000000
     }
   }
   ```**Data Rules:**
   - Dimension values MUST be 100% correct from the matrix — NOT random
   - Supporting fields → random + unique + traceable (prefix`auto_combo{XX}`)
   - Does not contain real PII

3. **⏸️ STOP — Present to the user:**
   - Complete combination matrix
   - Ask: "Are there any missing combinations? What needs to be added?"
   - **Waiting for user confirmation**

---

## Next step after this workflow

| Goal | Next Workflow |
|----------|-------------------|
| Generate detailed **test cases** for each combination |`/generate-manual-testcases-rbt`— input = matrix |
| Create **real test data on the system** via browser pipeline |`/generate-combinatorial-test-data` — mode PIPELINE |
| Sinh **automation scripts** | `/generate-automation-from-testcases`— input = test cases |

---

## Practical example

### Example 1: E-Commerce Order (DOCUMENT mode)

**User says:** "Order flow analysis: Select product → Select shipping → Payment → Confirmation"

**Agent performs:**
1. Extract 4 modules from the description
2. Dimensions: Product type (3), Shipping (3), Payment (4) = 36 full sets
3. Run script pairwise → reduce to ~12 sets
4. Output: Matrix of 12 sets + Module Map

### Example 2: Insurance Contract (BROWSER mode)

**User says:** "Inspect insurance app at https://example.com, contract creation flow"

**Agent performs:**
1. Navigate through 3 modules, snapshot each module
2. Extract dimensions from DOM: Insurance type (3), Package (4), Term (3), PTTT (2) = 72 full sets
3. Run script pairwise → reduce to ~15 sets
4. Output: Matrix of 15 sets + Data Flow

---

## STRICTLY PROHIBITED

| ❌ Do not do | ✅ Correct replacement |
|-------------------|-----------------| 
| Manual pairwise calculation (greedy/IPOG) | Generate Python + script`allpairspy`→ run script |
| Guess dimension values ​​without source | DOCUMENT: extract from document, BROWSER: inspect DOM |
| Fill in Expected Template/Formula yourself when you don't know | Leave blank or ask user — DO NOT write`[Need confirmation]`|
| Generate Full Cartesian by default when dimensions are large | Use Pairwise — 80-90% off combo sets |
| Ignore constraints (invalid combiner) | Must identify and eliminate invalid combinations |
| Loading too many skills (>2) into context | Only load 2 required skills, add other skills when needed |

---

## Final checklist

- [ ] Collected information for EACH module (via documents or browser)
- [ ] Built Dependencies Matrix between modules
- [ ] Fully extracted dimensions + values + constraints
- [ ] Selected the appropriate matrix strategy
- [ ] **(Pairwise)** Script generated and run — NO manual calculation
- [ ] Matrix contains only valid combinations (constraints removed)
- [ ] User has confirmed the matrix in Step 4
- [ ] Artifact output has been saved to the correct project location