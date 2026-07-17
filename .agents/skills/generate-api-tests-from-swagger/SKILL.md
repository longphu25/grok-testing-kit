---
name: generate-api-tests-from-swagger
description: Generate API test cases and automation scripts from Swagger/OpenAPI specification. Supports 2 modes — SPEC (test cases only) and FULL (test cases + automation scripts).
---

# Workflow: Generate API Tests from Swagger/OpenAPI

> **MANDATORY SKILL:** You MUST load and carefully read the content of the skill **`/qa-automation-engineer`** (in`.agents/skills/qa-automation-engineer/SKILL.md`) before starting. Additionally, refer to additional skills **`/test-data-generator`** to generate standard test data.

This workflow helps the agent analyze the Swagger/OpenAPI specification, identify endpoints, generate structured API test cases, and (depending on mode) automatically generate complete automation scripts.

## ⚠️ Implementation principles

- **All output in English**
- **DO NOT guess** schema/endpoint — must read actual spec (JSON/YAML)
- **Must wait for user confirmation** scope in Step 2 before generating details
- If the user has not provided Swagger URL/file → ask before starting
- ⚠️ **Rule E3:** When testing FAIL → read the log yourself → analyze → fix → run again. DO NOT ask the user during the error fixing process

## 2 Modes

| Mode | When to use | Output |
|---|---|---|
| **SPEC** (default) | User needs API test cases in document form | API Test Cases (Markdown) + Test Data Matrix |
| **FULL** | User requires automation scripts | Like SPEC + Automation Scripts + Project Structure |

> If the user says "generate automation", "write API test code", or request scripts → automatically switch to **Mode FULL**.

## Steps to perform

### Step 1: Receive & Analyze Spec (Parse & Analyze)

1. **Collect Swagger/OpenAPI spec** from users:
   - **Direct URL** (eg:`https://api.example.com/swagger.json`) → use`read_url_content`to fetch
   - **File local** (JSON/YAML) → use`view_file`to read
   - **Swagger UI URL** → extract the original spec URL (usually`/v2/api-docs`or`/v3/api-docs`)
   - **Scalar API Reference URL** → inspect HTML to find it`data-configuration`contains the spec URL (usually`/swagger/json`, `/reference/json`, or relative path in attribute`url`). VD: `https://book.anhtester.com/swagger`→ spec at`https://book.anhtester.com/swagger/json`- **Other Doc API types** (Redoc, Stoplight, RapiDoc) → find spec URL in page source or network requests
2. **Parse spec** and extract information:
   - Base URL, API version, authentication scheme (Bearer, API Key, OAuth2, Basic)
   - List of all endpoints:`method + path`- Request parameters: path, query, header, body (schema + required fields)
   - Response schemas: status codes, response body structure
   - Models/Definitions: reusable data models
3. **Classify endpoints** by group:
   - **CRUD operations** — Create, Read, Update, Delete
   - **Authentication** — Login, Register, Token refresh
   - **Business Logic** — Complex business processing APIs
   - **Utility** — Health check, config, metadata

### Step 2: Confirm Scope & Tech Stack (CHECKPOINT — ⏸️ STOP)

1. **Summary presentation** for user review:
   - Total number of detected endpoints (grouping)
   - Authentication method
   - List of endpoint groups + number of APIs per group
   - Recommended mode (SPEC or FULL)
2. **Ask user to confirm:**
   - "Do you want to test all endpoints or just focus on certain groups?"
   - "Do you want the output to be test cases (SPEC) or automation scripts (FULL)?"
   - If Mode FULL: "Desired Tech stack?" (default according to the table below)
3. **Wait for the user to confirm** the scope before going to Step 3

**Default Tech Stack (Mode FULL):**

| Frameworks | Language | When to use |
|---|---|---|
| **REST Assured** | Java | Default for Java projects, TestNG runner |
| **Playwright API Testing** | TypeScript | When users use Playwright or TypeScript stack |
| **Supertest + Jest** | TypeScript/JS | When users use Node.js backend |
| **Requests + Pytest** | Python | When users use Python stack |

### Step 3: Generate API Test Scenarios & Test Data

1. **For each endpoint** in the confirmed scope, generate test scenarios according to 7 types:
   - **✅ Happy Path** — Valid request, response with correct schema + status code
   - **❌ Negative — Validation** — Missing required fields, wrong data type, exceeding max length
   - **❌ Negative — Auth** — No token, expired token, wrong role token
   - **🔲 Boundary** — Min/max values, empty string, null, special characters
   - **⚡ Edge Cases** — Concurrent requests, duplicate creation, large payload, unicode/emoji
   - **🔒 Security** — SQL injection, XSS, IDOR, sensitive data exposure (see section 5 below)
   - **📄 Pagination & Filtering** — Pagination, sorting, searching (see section 6 below)

2. **For each scenario**, clearly define:
   - **Request:** Method, URL, Headers, Body/Params (specific values)
   - **Expected Response:** Status code, Response body structure, Error message
   - **Priority:** P1 (Critical) / P2 (High) / P3 (Medium) / P4 (Low)

3. **Generate Test Data Matrix** (use skill`/test-data-generator`):
   - Data valid for Happy Path
   - Invalid data for Negative cases (each field has 1 set of negatives)
   - Boundary values according to schema constraints (minLength, maxLength, min, max, pattern)
   - Data must be **unique + traceable** (eg:`auto_api_1712049200@test.com`)

4. **Field-Level Validation for Request Body (REQUIRED):**

   For each endpoint with a request body (POST/PUT/PATCH), the agent **MUST list each field** in the body and generate separate negative TCs for EACH field:

   | Field Type | Validation needs testing |
   |---|---|
   | **String (name, title...)** | Required → send missing · Empty string`""`· Whitespace only`"   "`· Exceed maxLength · Below minLength · Special characters`<>&"'` · Unicode/Emoji · XSS payload `<script>alert(1)</script>` · SQL injection `' OR 1=1--`|
   | **Email** | Wrong format (missing`@`, missing domain) · Duplicate email (if unique) · Max length · Case sensitivity |
   | **Number (age, price...)** | String instead of number · Negative number · Zero · Decimal (if integer) · Overflow (`999999999999`) · Min/Max value |
   | **Boolean** | Send string`"true"`instead of boolean`true`· Send`null`· Send number`1`/`0`|
   | **Date/DateTime** | Wrong format · Date does not exist (`2024-02-31`) · Future/past date (depending on rule) · Different timezone |
   | **Enum** | Value outside enum · Case sensitivity · Empty string |
   | **Array** | Empty array`[]`· Array too many elements · Elements of wrong type · Duplicate elements |
   | **Object (nested)** | Missing required sub-fields · Extra fields not in the schema · Nested object`null`|
   | **File (multipart)** | Wrong MIME type · Over capacity · Empty file (0 bytes) · File name with special characters |

   **Example — POST /api/customers`{ name, email, phone, age }`:**
   ```
   → Field "name" (string, required, maxLength: 100):
TC: Sending missing field name → 400 + error message
     TC: name = "" (empty) → 400
     TC: name = "   " (whitespace) → 400
TC: name = 101 characters → 400 (exceeds max)
     TC: name = "<script>alert(1)</script>" → 400 or sanitized
   
   → Field "email" (string, required, format: email):
     TC: email = "invalid" → 400
     TC: email = "test@" → 400
TC: email duplicates another user → 409 Conflict
   
   → Field "age" (integer, min: 0, max: 150):
     TC: age = "abc" → 400 (sai type)
TC: age = -1 → 400 (below min)
     TC: age = 151 → 400 (exceeds max)
     TC: age = 18.5 → 400 (decimal instead of integer)
   ```> **Principle:** Each field has its own schema → separate validation TCs. DO NOT combine multiple fields into 1 TC. Prioritize testing each field independently first, then test in combination.

5. **API Security Testing:**

   Generate security test cases for each endpoint:

   | Type | Test Scenarios |
   |---|---|
   | **Injection** | SQL injection in query params (`?id=1 OR 1=1`) · SQL injection in body fields · XSS in input fields · Command injection (if API handles shell) |
   | **IDOR** | Access another user's resource by ID (`GET /users/999`when the user only has permission to view user 123) · Change ID in PUT/DELETE to edit/delete resources that are not yours |
   | **Auth Bypass** | Call API without token → must 401 · Expired token → 401 · Low role token calls high role API → 403 · Token tampered (fix payload) → 401 |
   | **Sensitive Data** | Response does not return password/secret · Response does not leak internal IDs/stack traces · Headers do not leak server information (`X-Powered-By`, `Server`) |
   | **Rate Limiting** | Sending multiple requests continuously → must be limited (429) · Brute force login → lock account |
   | **CORS** | Check`Access-Control-Allow-Origin`header · Check preflight request (OPTIONS) |

6. **Pagination & Filtering Tests (for GET List endpoints):**

   | Test Type | Scenarios |
   |---|---|
   | **Pagination** | Do not pass page/limit → use default ·`page=1&limit=10`→ exactly 10 items ·`page=999`→ returns empty array (no error) ·`limit=0`or`limit=-1`→ reasonable handling ·`limit=10000`(too large) → limit or error |
   | **Sorting** |`sort=name&order=asc`→ results in correct order ·`sort=invalid_field`→ error or ignore · Case sensitivity in sort |
   | **Filtering** | Filter by each supported field · Filter with non-existent values ​​→ return empty array · Filter with special characters · Combine multiple filters at the same time |
   | **Search** | Search partial match · Search case-insensitive · Search with special characters · Search empty string |

7. **Distinguish between PUT and PATCH (if the API has both):**

   | Method | Test Focus |
   |---|---|
   | **PUT** | Submit all fields → update all · Submit missing optional field → that field is reset/null · Submit required field missing → 400 |
   | **PATCH** | Send only 1 field → only that field changes, other fields remain the same · Send empty body`{}`→ no change (or 400) · Submit field that does not exist → ignore or 400 |

### Step 4: Packaging API Test Cases (Output — Mode SPEC)

1. Create **artifact**`api_test_cases.md`with structure:
   - **API Overview** — Base URL, Version, Auth method, Total endpoints
- **Endpoint Catalog** — Table:`| # | Method | Path | Description | Number of Test Cases |`- **Detailed Test Cases** — By each endpoint:```
| TC ID | Endpoints | Scenario | Request | Expected Response | Priorities | Type |
   ```- **Test Data Matrix** — Valid/invalid/boundary data table for each model
   - **Dependencies & Execution Order** — Order of running tests (eg: must create user before test gets user)

2. If the user selects **Mode SPEC** → **END** here

### Step 5: Generate Automation Scripts (Mode FULL)

> Only execute when in **Mode FULL**

1. **Design project structure** in accordance with the framework:

   **REST Assured (Java):**```
   src/test/java/
   ├── api/                    # API client classes (per resource)
   │   ├── BaseApi.java        # Base config: baseURI, auth, logging
   │   ├── UserApi.java        # Methods: createUser(), getUser(), ...
   │   └── AuthApi.java        # Methods: login(), refreshToken(), ...
   ├── models/                 # POJO/DTO classes (from Swagger models)
   │   ├── UserRequest.java
   │   └── UserResponse.java
   ├── tests/                  # Test classes (TestNG)
   │   ├── UserApiTest.java
   │   └── AuthApiTest.java
   ├── utils/                  # Helpers
   │   ├── TestDataGenerator.java
   │   └── AssertionHelper.java
   └── testdata/               # Test data files (JSON/YAML)
   ```

   **Playwright API (TypeScript):**
   ```
   tests/
   ├── api/
   │   ├── helpers/
   │   │   ├── base-api.ts     # Base request context, auth
   │   │   ├── user-api.ts     # API methods per resource
   │   │   └── test-data.ts    # Data generators
   │   ├── user.api.spec.ts    # Test file per resource
   │   └── auth.api.spec.ts
   └── fixtures/
       └── api-fixtures.ts     # Shared fixtures (auth tokens, etc.)
   ```

   **Requests + Pytest (Python):**
   ```
   tests/
   ├── api/
   │   ├── conftest.py          # Fixtures: base_url, auth_token, api_client
   │   ├── helpers/
   │   │   ├── base_api.py      # Base API client (requests.Session)
   │   │   ├── user_api.py      # API methods per resource
   │   │   └── test_data.py     # Data generators
   │   ├── models/
   │   │   ├── user_model.py    # Pydantic/dataclass models
   │   │   └── response_model.py
   │   ├── test_user_api.py     # Test file per resource
   │   └── test_auth_api.py
   └── pytest.ini               # Pytest config
   ```

   **Supertest + Jest (TypeScript):**
   ```
   tests/
   ├── api/
   │   ├── helpers/
   │   │   ├── base-api.ts      # Supertest agent config
   │   │   ├── user-api.ts      # API methods per resource
   │   │   └── test-data.ts     # Data generators
   │   ├── models/
   │   │   └── user.model.ts    # TypeScript interfaces
   │   ├── user.api.test.ts     # Test file per resource
   │   └── auth.api.test.ts
   ├── jest.config.ts            # Jest config
   └── setup.ts                  # Global setup (auth token)
   ```2. **Code generation** in order:
   - **Base API class** — Config baseURL, default headers, auth interceptor, request/response logging
   - **Model/DTO classes** — From Swagger definitions/schemas
   - **API client classes** — Methods for each endpoint (return typed response)
   - **Test Data generators** — Data factory with random + traceable values
   - **Test classes** — Call API client, assert status code + response body + schema

3. **Required Assertions** for each test:
   - ✅ HTTP Status Code (exact match)
   - ✅ Response body — key fields matching expected values
   - ✅ Response time < threshold (if there is SLA)
   - ✅ Response schema validation (correct structure)
   - ✅ Error message is accurate for negative cases
   - ✅ Headers check (Content-Type, CORS if relevant)

4. **Best practices in code:**
   - Use **Builder pattern or Factory** for test data
   - **Chaining requests** when needing to setup data (eg: create → get → update → delete)
   - **Soft assertions** when you need to check many fields at the same time
   - **Parameterized tests** for data-driven scenarios
   - **Cleanup/teardown** — deletes created test data after testing is completed
   - No hardcode tokens/credentials — read from env or config

### Step 6: Run Test & Auto-Heal (Execution & Auto-Heal)

> Only execute when in **Mode FULL**

1. **Run test** with`run_command`:
   - REST Assured: `mvn test -Dtest=<TestClass>`
   - Playwright: `npx playwright test tests/api/`
   - Pytest: `python -m pytest tests/api/`2. **Monitor** the results`command_status`:
   - If **PASS** → update artifact, report results
   - If **FAIL** → apply Auto-Heal loop:```
   WHILE test FAIL:
1. Read the error log → determine the root cause
     2. Error classification:
        - Schema mismatch → update model/assertion
        - Auth failure → check token flow
        - 404/405 → check the endpoint path/method again
        - Timeout → increase timeout or check server
        - Data conflict → replace new unique test data
     3. Modify the code with `replace_file_content` or `multi_replace_file_content`
     4. Rerun the test
     5. Repeat until PASS (maximum 5 rounds)
   ```3. **⚠️ Rule E3:** DO NOT ask the user during the error fixing process. Only ask when:
   - Server API does not respond (down/blocked)
   - Business rules conflict with spec
   - 5 auto-heal rounds have passed but still failed

## Handling common errors

| Error | Cause | How to handle |
|---|---|---|
| Swagger URL returns HTML | The URL is Swagger UI, not the spec | Find the original spec URL (`/v2/api-docs`, `/v3/api-docs`, `/swagger.json`) |
| Scalar URL returns HTML | The URL is Scalar API Reference UI | Inspect HTML search`data-configuration`→ get field`url`→ append to base URL. Try:`/swagger/json`, `/reference/json`|
| Spec is empty or cannot be parsed | File corrupt or not in standard format | Ask the user to provide it again, try converting YAML ↔ JSON |
| Auth endpoint unknown | Spec does not document auth flow | Ask the user about the auth method and how to get the token |
| Response schema is different from spec | The actual API does not match the document | Note is a known issue, adjust test according to actual response |
| Rate limiting | The API has a request/minute limit | Add delay between tests or use retry logic |
| CORS blocked | Browser blocks cross-origin requests | Check`Access-Control-Allow-Origin`header, tested directly using HTTP client (not through browser) |
| 405 Method Not Allowed | Wrong call to HTTP method | Check the spec again — confirm the method is correct (PUT vs PATCH, POST vs PUT) |
| SSL/TLS Certificate Error | Self-signed certificate or expired | Add option skip SSL verification in test config (test environment only) |

## Output

### Mode SPEC
- Artifact`api_test_cases.md`:
  - API Overview (Base URL, Version, Auth)
  - Endpoint Catalog (summary table)
  - Detailed Test Cases (grouped by endpoint)
  - Test Data Matrix
  - Execution Order & Dependencies

### Mode FULL
- All Mode SPEC outputs, plus:
  - Project structure (in accordance with the framework)
  - Base API class + Auth helper
  - Model/DTO classes
  - API client classes (per resource)
  - Test classes with full assertions
  - Test data generators
  - Test run results (PASS/FAIL report)