# 📋 Quick Guide: Using AI-RBT in 6 Steps

## 🔀 Select Usage Stream

There are **2 separate streams**, depending on the AI ​​tool you are using:

### Flow 1: Antigravity (Slash Command) — Automatic

```
Type: /generate-manual-testcases-rbt + paste requirements
→ AI automatically runs 6 steps according to skill, stopping at the checkpoint to wait for you
→ NO need to copy-paste prompt templates
```

**Advantages:** Fast, automatic, agent remembers context throughout.
**Disadvantages:** Instructions are at a general level (not as detailed as prompt templates).

### Flow 2: Copy-Paste Prompt — Manual (ChatGPT / Claude / Any AI)

```
Copy prompt Step 1 → paste into chat → AI processes
→ Copy prompt Step 2 → paste → AI processing
→ ... repeat to Step 6
```

**Advantages:** Prompt is more detailed, has specific examples, and deeper suggestions.
**Disadvantages:** Must copy-paste manually 6 times.

---

## Flow 1: Antigravity — Quick prompt

```
/generate-manual-testcases-rbt

Project: [Project name]
Feature: [Feature name]
Objective: [Short description]

[Paste requirements/user stories here]
```

When the AI ​​stops at checkpoint, just answer the question or type:```
Continue to Step [X]
```

---

## Flow 2: Copy-Paste — Step-by-step instructions

| Step | Name | Prompt file | Wait User? |
|-------|-----|-------------|-----------|
| **1** | Context & Role-play | Copy`plans/manual/01_context_and_roleplay/prompt.txt`+ fill`[...]`| ✅ Waiting for confirmation |
| **2** | Analysis & QnA | Copy`plans/manual/02_analysis_and_qna/prompt.txt`| ✅ **Waiting for Q&A response** |
| **3** | Decomposition | Copy`plans/manual/03_decomposition/prompt.txt` | Review nhanh |
| **4** | Traceability | Copy `plans/manual/04_traceability/prompt.txt`| ✅ **Waiting for review scenarios** |
| **5** | RBT & TC Generation | Copy`plans/manual/05_rbt_and_tc_generation/prompt.txt`| Review results |
| **6** | Template Mapping | Copy`plans/manual/06_template_mapping/prompt.txt`| Copy table → Excel |

### Flow diagram:

```
[Step 1] Copy prompt + paste requirements
    ↓ AI confirms understanding → User confirms OK
[Step 2] Copy the analysis prompt
    ↓ AI asks questions → ⏸️ User answers each question
[Step 3] Copy prompt decay
    ↓  AI sinh Module list → User review nhanh
[Step 4] Copy prompt traceability
    ↓ AI generates scenarios → ⏸️ User review + additional
[Step 5] Copy prompt to generate TC
    ↓ AI generates detailed test cases → User review
[Step 6] Copy standardized prompt
    ↓ AI generates Markdown table → Copy to Excel/Jira ✅
```

---

## Ultimate Tips

1. **Step 2 is the most important** — Don't rush, answer each question the AI asks carefully
2. **Dividing modules when there are many** — In Step 5, if there are >5 modules, ask AI to generate each module
3. **Review before formatting** — In Step 5, review test cases before going to Step 6
4. **Use in the same conversation** — Run all 6 steps in **the same conversation** to let the AI keep the context
5. **More detailed Copy-Paste Stream** — If you need the highest quality, use Stream 2 (even when using Antigravity)