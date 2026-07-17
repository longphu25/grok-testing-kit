# Locator Selection Strategy (Applies to All Frameworks)

> Locator stability and readability determine the health of an automation framework.
> Core rule: NEVER select elements based on DOM structure associated with styling. Let's build a semantic attribute-based locator.

## 1. Master Priority Map

Order of priority from high to low:

1. Accessibility / Aria attribute (semantic, screen reader support)
2. Dedicated test properties (`data-testid`, `data-test`, `data-qa`)
3. Primary identifier attribute (`id`, `resource-id`, `name`)
4. Framework-specific semantic function (Playwright:`getByRole`, `getByLabel`...)
5. CSS Selector
6. XPath (last choice)

## 2. Stability Rules

Every locator must ensure:
- Only match **exactly 1 element** on the page (unique in scope).
- Survives interface changes — not affected by DOM layout changes (adding/removing wrapper divs, changing flexbox).

**Use is STRICTLY PROHIBITED:**
- Dynamic CSS class name/temporary hash (eg:`css-1n2xyz-btn`)
- Chain`nth-child`, `nth-of-type`when there are better options
- Auto-generated IDs by the framework (auto-generated IDs)
- Absolute XPath based on position (e.g.`//div[3]/div[2]/form/button`)

## 3. Locator Verification Process

Before putting the locator in the code, you must check:

1. Does the Locator match **exactly 1 element** in the DOM?
2. Is element match a user-interactive element? (not shadow DOM overlay)
3. Reload / navigate the page again — is the locator still correct?
4. Test on many page states (loading, loaded, with data, without data) — is the locator stable?

## 4. Locator According to Framework

For detailed locator details for each framework, see:
- Playwright:`.agents/rules/playwright_rules.md` (Section 3)
- Selenium: `.agents/rules/selenium_rules.md` (Section 1)
- Appium: `.agents/rules/appium_rules.md` (Section 1)
