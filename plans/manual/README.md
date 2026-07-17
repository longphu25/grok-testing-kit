# AI-DRIVEN RISK-BASED TESTING FRAMEWORK (AI-RBT)

**Goal:** 
Leverage the speed of AI to execute details, combined with human RBT (Risk-Based Testing) strategic thinking to optimize testing resources.

## 📌 Core Principles

1. **Human Strategy:** Humans determine strategy, risk levels, and standards.
2. **AI Execution:** AI performs analysis, writes TCs and reviews vulnerabilities.
3. **Human Verification:** Humans check the results of AI before finalizing.

---

## 🚀 Overview Process (6 Steps)

1. **Context & Role-play (Create context):** Create a QA/Tester expert mindset for AI by defining roles and providing project context.
2. **Analysis & QnA (Requirements Analysis):** AI reads documents and analyzes requirements to clarify ambiguous points (Ambiguity) before writing scenarios.
3. **Decomposition:** Decompose the system into smaller Modules (Feature Mapping - FM) for easy evaluation.
4. **Traceability (Ensure coverage):** Set up a traceability matrix to ensure the required coverage (Coverage).
   - *(Note: After step 4 there may be a Checkpoint Risk Assessment step performed by humans - Human Only).*
5. **RBT & TC Generation (Detailed Test Case Generation):** Apply Risk-Based Testing strategy to have AI generate test scenario content (Logic & Scenario).
6. **Template Mapping (Format Standardization):** Standardize the entire Test Case format and fill in the template file/table for management use (eg Jira, Excel).

*(Each step above corresponds to 1 subfolder in this folder, incl`README.md`detailed instructions and`prompt.txt`contains sample commands for AI).*

---

## ⚠️ Important Note: Execution Strategy

For **Manual Testing** (Going from Functional Requirements / Figma), you are **REQUIRED to manually run each prompt sequentially** instead of combining them into one Workflow command that runs 100% automatically.

**Reason:**
1. **Bottleneck in Step 2 (Analysis & QnA):** AI needs time to analyze and ask questions (Ambiguities) about the logic of Requirement for you to answer. If it runs all at once, the AI ​​will guess the logic, leading to writing seriously incorrect Test Cases.
2. **Human in the loop:** In Steps 4 and 5, the human (Tester) must review the risk assessment (RBT) themselves before allowing the AI ​​to generate detailed scenarios.
3. **Anti-Hallucination:** Loading a long document and forcing the AI ​​to spit out hundreds of scenarios at the same time will cause the AI ​​to lose focus and miss the flow (Miss coverage). Breaking each prompt into smaller pieces will bring the absolute best Test Case quality.