---
name: generate-locator
description: Generates a stable locator for UI elements. Supports Playwright, Selenium, Appium.
---

# /generate-locator — Generate Stable Locator for UI Automation

> User provides the element to find the locator (description, screenshot, URL, or HTML snippet).
> AI inspects the actual DOM/UI hierarchy, generates stable locators according to standard priorities, verifies uniqueness, returns results.

> **MANDATORY:** Before starting, MUST load and read carefully:
> - **Skill:**`.agents/skills/smart-locator-agent/SKILL.md`— Locator generation process
> - **Skill:**`.agents/skills/ui-debug-agent/SKILL.md`— DOM inspection process
> - **Rule:**`.agents/rules/locator_strategy.md`— Locator priority map
> - **Rule:**`.agents/rules/<framework>_rules.md`— Rules specific to the framework in use

---

## Input needed from User

| Input | Required | Description |
|-------|----------|-------|
| Description element | ✅ | For example: "Login button", "Country selection dropdown", "Email input box" |
| The page URL contains the | element ✅ | Let AI navigate and inspect the actual DOM |
| Frameworks | ✅ |`playwright`, `selenium`, or`appium`|
| HTML snippet | ❌ | If the User already has a DOM context — AI can be used for quick analysis |
| Destination page class | ❌ | The Page class file to which the locator will be added |
| Login required | ❌ | If the page requires login — User indicates how to login |

> **Note about Login:** If the page requires login, the User MUST specify how to login (fixture, script, login URL...). NO ONE SHOULD read it themselves`.env`or guess credentials.

---

## Steps to perform

### Phase 1: Requirements analysis

1. **Understand the element to look for** — Clearly define:
   - **Element type:** button, input, link, dropdown, dialog, table row, checkbox, radio...
   - **Context:** Located in main page, dialog/modal, sidebar, table, iframe?
   - **Operation:** click, fill, select, hover, verify text, verify visibility?

2. **Define the framework and read the corresponding rules:**

   | Frameworks | Rule file |
   |-----------|-----------|
   | Playwright |`.agents/rules/playwright_rules.md` |
   | Selenium | `.agents/rules/selenium_rules.md` |
   | Appium | `.agents/rules/appium_rules.md`|

3. **Check the current Page class (if specified by the User):**
   - Read the Page class file → know the locator is available
   - Avoid duplication or naming conflicts

---

### Phase 2: Inspect the actual DOM/UI Hierarchy

> ⚠️ **IMMORTAL PRINCIPLES: NEVER GUESS THE LOCATOR. MUST INSPECT REALITY.**

#### 2A. Web (Playwright MCP)

4. **Navigate to the page containing the element:**```
   browser_navigate(url=<target_url>)
   ```5. **Resize viewport (REQUIRED):**```
   browser_resize(width=1920, height=1080)
   ```

6. **Capture DOM:**
   ```
   browser_snapshot()
   ```7. **Analyze elements in snapshots:**
   - Find ref element in DOM tree
   - Record **all valid attributes**:`role`, `aria-label`, `aria-labelledby`, `data-testid`, `data-test`, `data-qa`, `id`, `name`, `placeholder`, `type`, `href`, text content
   - Record **parent context** (dialog? table? sidebar? iframe?)

8. **If element is hidden** (dropdown menu, modal, tooltip...):
   - Perform action to open element:`browser_click(ref=<trigger>)`- Capture again:`browser_snapshot()`#### 2B. Mobile (Appium)

4. **Use Appium Inspector** or`page_source`to get UI hierarchy
5. **Record attributes:**`accessibility-id`, `resource-id`, `content-desc`, `text`, `class`, `bounds`6. **If element is in scroll view** → scroll to element before inspecting

---

### Phase 3: Generate locator according to Priority

9. **Apply Master Priority Map:**

   | # | Type | When to use |
   |---|-------|-------------|
   | 1 | Accessibility / Aria | Element does`role`, `aria-label`clear |
   | 2 | Test attributes | Element does`data-testid`, `data-test`, `data-qa`|
   | 3 | ID/name | Element does`id`or`name`stable (not auto-generated) |
   | 4 | Semantic Framework | Playwright:`getByRole`, `getByLabel`... |
   | 5 | CSS Selector | Use specific attributes, avoid dynamic classes |
   | 6 | XPath | Last option — only use relative XPath |

10. **Generate locator according to framework:**

    **Playwright (TypeScript/JavaScript):**```typescript
    // Priority 1: Role-based
    page.getByRole('button', { name: 'Submit' })

    // Priority 2: Test ID
    page.getByTestId('submit-btn')

    // Priority 3: Label / Placeholder
    page.getByLabel('Email')
    page.getByPlaceholder('Enter your password')

    // Priority 4: Text
    page.getByText('Submit')

    // Priority 5: CSS
    page.locator('#submit-button')
    page.locator('[data-testid="submit-btn"]')

    // Priority 6: XPath (cuối cùng)
    page.locator('//button[@type="submit"]')
    ```

    **Playwright (Python):**
    ```python
    # Priority 1: Role-based
    page.get_by_role("button", name="Submit")

    # Priority 2: Test ID
    page.get_by_test_id("submit-btn")

    # Priority 3: Label / Placeholder
    page.get_by_label("Email")
    page.get_by_placeholder("Enter your password")

    # Priority 4: Text
    page.get_by_text("Submit")

    # Priority 5: CSS
    page.locator("#submit-button")

    # Priority 6: XPath (cuối cùng)
    page.locator("//button[@type='submit']")
    ```

    **Selenium (Java):**
    ```java
    // Priority 1: ID
    driver.findElement(By.id("submit-button"));

    // Priority 2: Test attribute
    driver.findElement(By.cssSelector("[data-testid='submit-btn']"));

    // Priority 3: Name
    driver.findElement(By.name("submit"));

    // Priority 4: CSS Selector
    driver.findElement(By.cssSelector("button.btn-primary[type='submit']"));

    // Priority 5: XPath (cuối cùng)
    driver.findElement(By.xpath("//button[@type='submit']"));
    ```

    **Appium (Java):**
    ```java
    // Priority 1: Accessibility ID
    driver.findElement(AppiumBy.accessibilityId("login_button"));

    // Priority 2: Resource ID (Android)
    driver.findElement(AppiumBy.id("com.app:id/login_button"));

    // Priority 3: iOS Predicate
    driver.findElement(AppiumBy.iOSNsPredicateString("label == 'Login'"));

    // Priority 4: Class Chain (iOS)
    driver.findElement(AppiumBy.iOSClassChain("**/XCUIElementTypeButton[`label == 'Login'`]"));

    // Priority 5: XPath (cuối cùng)
    driver.findElement(AppiumBy.xpath("//android.widget.Button[@text='Login']"));
    ```---

### Phase 4: Validate locator

11. **Verify uniqueness — MUST match exactly 1 element:**

    **Web (Playwright MCP):**```
    browser_evaluate(function="() => document.querySelectorAll('<css_selector>').length")
    ```

    **Selenium:**
    ```java
    List<WebElement> matches = driver.findElements(By.<strategy>("<locator>"));
    assert matches.size() == 1;
    ```

    **Appium:**
    ```java
    List<WebElement> matches = driver.findElements(AppiumBy.<strategy>("<locator>"));
    assert matches.size() == 1;
    ```12. **Verify visibility** — Element must be interactive:
    - Not overlayed by other elements
    - Not in state`hidden`, `display:none`, `visibility:hidden`- Not outside the viewport (need to scroll)

13. **Verify stability — Check checklist:**
    - [ ] Do not use dynamic CSS class (eg:`css-1n2xyz`, `sc-bdnxRM`)
    - [ ] Do not use absolute XPath (eg:`//html/body/div[1]/div[2]/button`)
    - [ ] Do not use auto-generated ID (eg:`ember123`, `react-select-2-input`)
    - [ ] Not used`nth-child` / `nth-of-type`when there are better options
    - [ ] Survive the page reload
    - [ ] Stable across many page states (loading, loaded, with data, without data)

---

### Phase 5: Return results

14. **Output Format — REQUIRED to provide all 3 parts:**```markdown
## Locator Result: [Element description]

**Framework:** [Playwright / Selenium / Appium]

### 🎯 Primary Locator (Recommended)
```<language>
// Locator code — copy-paste ready
```
- **Type:** [Role-based / Test ID / CSS / ...]
- **Unique:** ✅ Match 1 element
- **Stability:** ✅ Do not use dynamic class / absolute xpath

### 🔄 Fallback Locator
```<language>
// Replace locator when primary fails```
- **Type:** [CSS / XPath / ...]
- **When to use:** When the primary locator breaks due to DOM changes

### 💡 Reasoning
- Explain why you chose this Primary locator
- Why eliminate other candidates?
- Potential risks (if any)

### 📋 Usage Example (if requested by User)
```<language>
// Example of using locator in test code```
```15. **(Optional) If User requests to add Page class:**
    - Add the locator to the correct position in the Page class
    - Name according to the project's naming convention
    - Create methods using locators if necessary

---

## Common Patterns (Reference)

### Scoping locator in Dialog / Modal:```typescript
// Playwright — scope vào dialog trước
const dialog = page.getByRole('dialog');
dialog.getByRole('button', { name: 'Confirm' }).click();
```
```java
// Selenium — scope vào dialog
WebElement dialog = driver.findElement(By.cssSelector("[role='dialog']"));
dialog.findElement(By.cssSelector("button[data-testid='confirm']")).click();
```

### Dynamic text matching:
```typescript
// Playwright — exact vs partial
page.getByText('Submit', { exact: true })     // exact match
page.getByText(/submit/i)                      // regex, case-insensitive
```
```python
# Playwright Python — normalize-space XPath
page.locator(f"//a[normalize-space()='{text}']")
```

### Table row action:
```typescript
// Playwright — filter row rồi interact
const row = page.getByRole('row').filter({ hasText: 'John Doe' });
row.getByRole('button', { name: 'Edit' }).click();
```
```java
// Selenium — XPath relative trong table
driver.findElement(By.xpath("//tr[contains(., 'John Doe')]//button[text()='Edit']"));
```---

## STRICTLY PROHIBITED

| ❌ Do not do | ✅ Correct replacement |
|-------------------|-----------------|
| Guess the locator doesn't inspect DOM/UI |`browser_snapshot()`or Appium Inspector first |
| Use dynamic CSS classes (`css-1n2xyz`, `sc-xxx`) | Use role, aria, data-testid, text |
| Use absolute XPath (`//html/body/div[1]...`) | Use relative XPath with attribute |
| Use auto-generated ID (`ember123`, `:r1:`) | Use stable attribute or text |
| Return locator does not verify uniqueness | Always verify match exactly 1 element |
| Only pay for 1 locator, no fallback | Return Primary + Fallback + Reasoning |
| Read`.env`to get login credentials | Ask the User how to login or use an existing fixture |

---

## Final checklist

- [ ] Inspected the actual DOM/UI hierarchy (no guessing)
- [ ] Locator according to priority: accessibility > test-id > id/name > semantic > css > xpath
- [ ] Primary locator matches exactly 1 single element
- [ ] There is a Fallback locator
- [ ] There is Reasoning explaining the reason for choosing
- [ ] Do not use dynamic class, absolute xpath, auto-generated ID
- [ ] Locator is stable through page reload and many states
- [ ] (If added to Page class) Verified code runs without errors