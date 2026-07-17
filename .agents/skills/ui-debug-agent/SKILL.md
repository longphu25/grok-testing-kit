---
name: ui-debug-agent
description: Skill to inspect web/mobile applications using browser tools, analyze DOM elements, identify stable locators, debug UI automation failures, and support generation of Page Object classes.
---

# UI Debug Agent

## Description

Specialized skills help agents inspect web/mobile applications directly on real browsers, analyze DOM, collect stable locators, and debug UI automation issues.

Agents can:

- Open a real browser, navigate to any URL
- Inspect DOM elements — determine attributes, hierarchy, state
- Collect stable locators for Playwright, Selenium, Appium
- Debug automation failures (element not found, click intercepted, timeout)
- Capture UI state (snapshot, screenshot) for analysis
- Analyze dynamic content, iframes, shadow DOM, SPA navigation

---

## When to Use

Use this skill when:

- Need to **explore the UI** of a new website/module
- Need to **find locator** for specific element
- Need to **debug** test automation fails due to UI changes
- Need to **verify** whether the locator works on the actual DOM
- Need to **analyze DOM** to understand UI structure (forms, tables, modals)
- Need **capture evidence** (screenshot) for test report

Trigger keywords: "inspect UI", "find locator", "debug element", "open browser view", "inspect DOM"

---

## MCP Command Sequence (REQUIRED)

When using Playwright MCP to debug UI, **ALWAYS** follow this order:

```
1. browser_navigate(url) → Open page
2. browser_resize(1920, 1080)      → Desktop viewport
3. browser_wait_for(text/time) → Wait for page load
4. browser_snapshot() → Collect DOM (used to analyze + find locator)
5. browser_click/type/hover(ref) → Interaction (if needed)
6. browser_take_screenshot() → Take a photo (evidence upon failure or milestone)
```

### Important rules:

| Rules | Details |
|---|---|
| **DO NOT navigate again** if already on the correct page | Avoid unintentional reloads |
| **ALWAYS resize** immediately after navigate |`browser_resize(1920, 1080)`— ensure desktop viewport |
| **ALWAYS wait** before snapshot | Wait for the page to load completely |
| **Use snapshots for analysis** | Snapshot returns accessibility tree — fast, accurate, yes`ref`to interact |
| **Use screenshot for reporting** | Screenshot is an image — used when visual evidence is needed |

---

## Snapshot vs Screenshot

| | `browser_snapshot` | `browser_take_screenshot`|
|---|---|---|
| **Returns** | Accessibility tree (text + ref IDs) | Image (PNG/JPEG) |
| **Purpose** | Analyze DOM, find locator, identify element | Visual evidence, reports, debug layout |
| **When to use** | ⭐ Always use before interacting | Only if there is a failure or an important milestone |
| **There are refs to interact** | ✅ Yes — use ref to click/type/hover | ❌ No — just an image |
| **Speed** | Fast | Slower |

**Rules:** Priority`snapshot`for analysis and use`screenshot` cho evidence.

---

## Inspect UI process

### 1. Open & Prepare Page

```
browser_navigate → URL target
browser_resize → 1920 × 1080
browser_wait_for → wait for the indicator page to load (text or time)
```

If the page requires login:
- Ask user credentials OR use an existing fixture in the project
- **DO NOT read files`.env`live** (security rules)

### 2. Collect DOM Structure

```
browser_snapshot → accessibility tree
```

From the snapshot, determine:
- **Main elements:** buttons, inputs, links, headings, tables
- **Important Attributes:** role, name, label, placeholder, testid
- **Hierarchy:** parent → child relationships
- **State:** visible, enabled, disabled, checked, expanded

### 3. Identify Locators

For each element that needs a locator, apply **priority order** according to the framework:

**Playwright:**

| Priorities | Locator | Example | When to use |
|---|---|---|---|
| 1 ⭐ |`getByRole()` | `getByRole('button', {name: 'Submit'})`| Element has a clear role + accessible name |
| 2 |`getByLabel()` | `getByLabel('Email')`| Form input has label |
| 3 |`getByPlaceholder()` | `getByPlaceholder('Enter email')`| Input has placeholder, no label |
| 4 |`getByText()` | `getByText('Welcome back')` | Text content unique |
| 5 | `getByTestId()` | `getByTestId('submit-btn')`| Element has data-testid attribute |
| 6 | CSS |`page.locator('.submit-button')`| There are no matching semantic options |
| 7 | XPath |`page.locator('//div[@class="x"]')`| Last resort — avoid using |

**Selenium:**

| Priorities | Locator | Example |
|---|---|---|
| 1 ⭐ |`By.id()` | `By.id("email")` |
| 2 | `By.cssSelector("[data-testid]")` | `By.cssSelector("[data-testid='submit']")` |
| 3 | `By.name()` | `By.name("username")` |
| 4 | `By.cssSelector()` | `By.cssSelector(".login-form button")` |
| 5 | `By.xpath()` | `By.xpath("//button[text()='Login']")` |

**Appium (Mobile):**

| Priorities | Locator | Example |
|---|---|---|
| 1 ⭐ | AccessibilityID |`MobileBy.accessibilityId("loginButton")` |
| 2 | ID (resource-id) | `MobileBy.id("com.app:id/login_btn")` |
| 3 | Name | `MobileBy.name("Login")` |
| 4 | XPath (relative) | `MobileBy.xpath("//android.widget.Button[@text='Login']")` |

### 4. Verify Locator

After defining the locator, **verification is required** on the actual DOM:

```
browser_snapshot → find element by ref
browser_click/type(ref) → try interactively
browser_snapshot → confirm results
```

**Locator is accepted when:**
- [ ] Unique on page (only match 1 element)
- [ ] Stable after many reloads
- [ ] Does not contain dynamic classes (css-xxx, sc-xxx, MuiXxx-root)
- [ ] Does not contain positional xpath (//div[3]/button[2])
- [ ] Does not depend on auto-generated attribute

---

## Handle special situations

### The page requires login
- Use the existing login fixture in the project or ask for user credentials
- **DO NOT read .env directly**
- After logging in, navigate to the page you want to inspect

### Modal / Dialog / Popup
- Modal is usually an overlay on the main page
-`browser_snapshot`will see modal content in the accessibility tree
- Interact with modal elements using refs from snapshots
- Wait for modal animation to complete before interacting

### Iframe
- `browser_snapshot`may not see content in iframe
- Use`browser_evaluate`to switch into iframe:```javascript
  () => document.querySelector('iframe').contentDocument.body.innerHTML
  ```
- Or use Playwright frame locator: `page.frameLocator('#iframe-id')`

### Shadow DOM
- Playwright `locator()`automatically pierce shadow DOM
- Selenium needed`shadowRoot.findElement()`
- `browser_snapshot`You can see shadow DOM content depending on MCP version

### Dynamic Content (SPA / AJAX)
- Wait for the content to load properly`browser_wait_for(text)`before snapshot
- If content loads lazy → scroll down first, then snapshot
- If content changes over time → take snapshots multiple times

### Tables / Lists (many repeating elements)
- Define pattern locator for row/cell
- Use`nth()`or`filter()`to target specific elements
- Playwright example:`page.getByRole('row').filter({hasText: 'John'}).getByRole('button', {name: 'Edit'})`

### Covered Element (Overlay / Toast)
- Check z-index, opacity, visibility in the DOM
- Wait for the overlay to disappear:`browser_wait_for(textGone: 'Loading...')`
- If toast notification covers the button → wait for toast timeout

---

## Anti-Patterns (FORBIDDEN)

| ❌ Wrong | ✅ Yes | Reason |
|---|---|---|
| Guess locator from feature name | Inspect the actual DOM then get the locator | Locator 100% accurate |
| Use screenshot to select locator | Use snapshot (accessibility tree) | Snapshots have refs, screenshots do not |
| Copy locator from old code without verification | Always verify locator on current browser | DOM may have changed |
| Use dynamic classes`.css-1abc`| Use role/label/testid | Dynamic class changes every build |
| Use positional xpath`//div[3]`| Use relative xpath or CSS | Positional xpath fragile |
| Take screenshots continuously | Only screenshot when fail or milestone | Waste of resources, slow |
| Navigate again when you are on the correct page | Only navigate when you need to change the URL | Avoid unnecessary reloads |

---

## Output

This skill can return:

- **Locator recommendations** — primary + fallback table for each element
- **DOM analysis** — element structure, attributes, state, hierarchy
- **Page Object suggestions** — appropriate class structure for the inspected page
- **Screenshots** — visual evidence at milestones
- **Debug findings** — cause of element not found / click failure + how to fix

---

## Rules References

Agent MUST comply with detailed rules:

- `.agents/rules/locator_strategy.md` — Master locator priority map
- `.agents/rules/playwright_rules.md` — Playwright browser setup and locator rules
- `.agents/rules/selenium_rules.md` — Selenium locator and wait rules
- `.agents/rules/appium_rules.md` — Appium mobile locator rules
- `.agents/rules/automation_rules.md` — General automation best practices