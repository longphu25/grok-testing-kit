# Step 5: Generate detailed Test Cases (RBT & Test Case Generation)


---

## Purpose

Generate detailed Test Cases based on the **Risk-Based Testing (RBT)** strategy: high risk → thorough testing, low risk → basic testing.

## How to use

1. Make sure to review and confirm the scenarios in Step 4.
2. Send files`prompt.txt`for AI, customize section`[Prompt]`necessary.
3. AI will generate complete test cases: Title, Pre-condition, Steps, Expected Result, Test Data, Risk Level, Priority.
4. Review results → go to Step 6.

## Important tip

### Split into multiple modules
If Step 4 has >5 modules, **don't tell the AI to spawn all at once**. Instead:```
Let's create Test Cases for Module 1 and Module 2 first.
```
After completing the review, continue:
```
Continue generating Test Cases for Module 3 and Module 4.
```

### Test Data must be specific
AI will generate simulated test data close to reality instead of placeholders:
- ✅`test_customer_01@domain.com`instead of ❌`valid email`
- ✅ `KH-2026-0012`instead of ❌`valid code`

### Risk Level determines the depth
| Risk Level | Expected TC number | Covered |
|-----------|----------|-------|
| **High** | 8-15+ TC/module | Happy + Negative + Boundary + Edge |
| **Medium** | 4-8 TC/module | Happy + Main Negative |
| **Low** | 2-4 TC/module | Basic happy path |

### Test Case design techniques

Prompt has integrated 4 classic techniques for AI to systematically generate test cases:

| Engineering | When to use | Example |
|----------|-------------|-------|
| **Equivalence Partitioning** | The field has many input types | Age: <18, 18-60, >60 |
| **Boundary Value Analysis** | Field has min/max | Password 6-20 characters: test 5,6,20,21 |
| **Decision Table** | Multiple-condition combination logic | Login: email + password + active status |
| **State Transition** | Object has workflow/state | Order: New → Processing → Delivered → Complete |