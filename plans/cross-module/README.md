# AI-DRIVEN CROSS-MODULE TESTING FRAMEWORK

**Goal:**
Analyze and test complex features across **many sequential modules**, where the output depends on **multi-dimensional combination of conditions** (Combinatorial Testing).

## 📌 Math Problem Solving

When a feature **does NOT fit in one module** but has to go through a chain of modules, with each module having many options — and the combination of options that determines the final output (different templates, formulas, business rules).

**Practical example:**

| Features | Dimensions |
|-----------|-------|
| Partner payment minutes | Partner type × Payment type × Tax × Debt × Asset source |
| Insurance contract | Insurance type × Subject × Package × Term × Payment method |
| Export orders | Market × Product type × Shipping × Payment × Documents |
| Approval process | Request Type × Department × Level × Amount → Different approval flow |

**If each dimension has 3-5 values ​​→** Full Cartesian combinations easily amount to **hundreds of combinations**.

---

## 🚀 2-Phase Process

### Phase 1: Analysis & Matrix Generation (`/generate-cross-module-test-plan`)

| Step | Name | Description | Wait User? |
|-------|-----|-------|-----------|
| **1** | Multi-Module Recon | AI opens the browser to explore each module, collecting fields + values ​​| ❌ |
| **2** | Data Flow Mapping | Determine what module A output → input module B | ✅ **Checkpoint** |
| **3** | Dimension Extraction | List all combined "dimensions" + values ​​+ constraints | ❌ |
| **4** | Combinatorial Matrix | Generating combination matrices (Pairwise / Business-critical / Full) | ❌ |
| **5** | Expected Output Mapping | Map expected template + formula for each set | ✅ **Checkpoint** |

**Main output:** Combined matrix table — ready to import Excel/Jira.

### Phase 2: Test Data Generation (`/generate-combinatorial-test-data`)

| Mode | When to use | Output |
|-------|-------------|--------|
| **GENERATE** | Generate data offline (JSON/CSV/Code) | The test data file has the structure |
| **PIPELINE** | Runs through the browser to create data on the system | Real data + IDs + screenshots |

---

## 3 Matrix Strategy

| Strategy | Description | When to use | Example |
|-----------|-------|-------------|-------|
| **Pairwise** (Default) | Cover 100% of pairs between any 2 dimensions | Large combination (>50) | 216 sets → ~20 sets |
| **Business-Critical** | Select only the most important set according to risk | Need focus, limited time | 216 sets → ~10 sets |
| **Full Cartesian** | Test ALL valid combinations | Critical system (finance, health) | 216 sets → 216 sets |

> 💡 **Pairwise Testing** reduces the number of combinations by 80-90% while still detecting the majority of errors.

---

## 🔗 Complete End-to-End Stream

```
┌─────────────────────────────────────────────────────────────────┐
│                    CROSS-MODULE TESTING FLOW                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│ 📋 Step 0: Analyze Requirements for each Module │
│ Workflow: /generate-requirements-from-website (run N times) │
│                          ↓                                       │
│ 📊 Step 1: Cross-Module Analysis & Matrix Generation │
│ Workflow: /generate-cross-module-test-plan ← NEW │
│ Output: Data Flow Map + Association Matrix │
│                          ↓                                       │
│ 🗃️ Step 2: Generate Test Data For Matrix │
│ Workflow: /generate-combinatorial-test-data ← NEW │
│ Output: Test data set (offline or on the system) │
│                          ↓                                       │
│ 📝 Step 3: Generate Detailed Test Cases │
│      Workflow: /generate-manual-testcases-rbt (FULL RBT)        │
│ Input: Matrix + Requirements │
│ Output: Full test cases │
│                          ↓                                       │
│ 🤖 Step 4: Generate Automation Scripts │
│      Workflow: /generate-automation-from-testcases              │
│      Output: Scripts PASS stable                                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 Folder structure

```
plans/cross-module/
├── README.md ← Introduction + Overview (you are reading this file)
└── QUICK_START.md ← Quick start guide (sample prompt + run flow)
```

**Workflow skills reference:**

```
.agents/skills/
├── generate-cross-module-test-plan/SKILL.md ← Analysis + Matrix
└── generate-combinatorial-test-data/SKILL.md      ← Sinh test data
```

**Extended skills:**

```
.agents/skills/test-data-generator/SKILL.md   ← Multi-Step Pipeline + Combinatorial Data
```

---

## 📋 Quick guide

Xem file `QUICK_START.md`in this folder to get started quickly.