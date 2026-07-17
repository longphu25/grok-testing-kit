# Step 3: System decomposition (Decomposition Strategy)


---

## Purpose

AI instructions break down complex systems/features into multiple **Modules** or **Sub-modules** that are easy to manage. Avoid the situation where AI is "overwhelmed" with information → writing flawed test cases.

## How to use

1. Make sure the questions in Step 2 have been fully answered.
2. Send files`prompt.txt`for AI.
3. AI will return:
   - List of **Modules / Sub-modules** with function descriptions
   - **Dependencies** (dependency relationships between modules)
4. Quickly review the results → go to Step 4.

## Decomposition strategy

There are 3 ways to decompose, depending on the project:

| How | Suitable when | Example |
|-------|-------------|-------|
| **According to UI** | The page has many clear sections | Header, Sidebar, Data Table, Form Popup |
| **By thread** | Features many CRUD operations | Flow Create, Flow Edit, Flow Delete |
| **By subject** | The system has many entities | User Management, Product Management, Order Management |

Customize section`[Hint]`in prompt.txt for AI to decompose in the most appropriate way.