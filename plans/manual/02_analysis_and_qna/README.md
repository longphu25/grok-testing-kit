# Step 2: Requirements Analysis and Q&A (Requirement Analysis)

---

## Purpose

Ask AI to analyze documents from Step 1 to find omissions, contradictions, and ambiguities **before** writing the script. This step simulates the mindset of a Tester reading documents and asking questions to the BA/PO.

## How to use

1. Copy content`prompt.txt`and send immediately after AI has confirmed in Step 1.
2. AI will return:
   - List of **threads** (Happy / Alternate / Exception Paths)
   - List of **Ambiguities** (detected blur points)
   - Numbered list of **Q&A questions**
3. **Read carefully** each question and answer for AI.
4. Once all the answers have been answered → go to Step 3.

## ⚠️ Important note

- **This is the most important step** in the process. If ignored, the AI ​​will guess the logic → the test case is seriously wrong.
- Answer **as specifically as possible**. If you don't know, clearly state "Undetermined, assumed to be..." so that AI can take note.
- Additional suggestions can be added to the section`[...]`in prompt.txt so that AI focuses on the areas you are interested in.

