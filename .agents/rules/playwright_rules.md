# Playwright Specific Rules

> Applicable when setting up and running automation with Playwright (TypeScript or Java).

## 1. Browser Settings (REQUIRED)

- **Viewport debug:** All UI debugging processes must run with the desktop viewport: **`1920x1080`**.
- **Playwright MCP — Required Resize:** When using Playwright MCP to debug UI, **ALWAYS** call`browser_resize(width=1920, height=1080)`**immediately after opening the browser** (after command`browser_navigate`Firstly). This is a mandatory step and cannot be skipped.```
Required order:
  1. browser_navigate(url) → open the page
  2. browser_resize(width=1920, height=1080) → set viewport
3. browser_snapshot() or browser_take_screenshot() → start inspect
  ```- **Headed mode:** Requires opening of a headed browser during setup and debug testing.
- **Headless mode:** Only allowed to use when:
  - Test has debug PASS 100% on headed mode
  - Or in the default CI/CD pipeline

## 2. Develop & Find Element Workflow

- Preferably use **Playwright MCP** to open the browser and interact with the target page.
- **Inspect actual DOM:** Verify and capture selectors directly from the browser DOM.
- **ABSOLUTELY NO:**
  - Speculative locator
  - Copy locator blindly from old code without verifying
  - Based on URL/document without claiming existence on real UI

## 3. Playwright Locator Priority Order

Playwright provides a user-oriented semantic locator. Prefer to use instead of CSS/XPath:

1.`getByRole()`— Best for semantic elements (buttons, links, headings...)
2.`getByLabel()`— Best for form fields with labels
3.`getByPlaceholder()`— Best for inputs with placeholder text
4.`getByText()`— Best for text content
5.`getByTestId()`— Best when element exists`data-testid`
6. `locator("css")`— Fallback when there are no better options

For example:```typescript
// Đúng — Semantic locator
page.getByRole('button', { name: 'Đăng nhập' })
page.getByLabel('Email')
page.getByPlaceholder('Nhập mật khẩu')

// Sai — XPath/CSS thô khi có semantic thay thế
page.locator('//button[@class="btn-login"]')
page.locator('.form-input:nth-child(2)')
```## 4. Wait Strategy

**STRICTLY PROHIBITED:**
-`page.waitForTimeout()` — hard sleep
- `await new Promise(r => setTimeout(r, N))`— create delay yourself
- Any way fixed timeout

**USE:**
- Take advantage of Playwright's default auto-waiting
- Web-First Assertions:```typescript
  await expect(locator).toBeVisible();
  await expect(locator).toBeEnabled();
  await expect(locator).toHaveText('Thành công');
  await expect(page).toHaveURL(/dashboard/);
  ```- For use only`waitForSelector()` khi `expect()`does not meet special requirements

## 5. Test Structure```typescript
test.describe('Tên Module', () => {
  test.beforeEach(async ({ page }) => {
    // Setup: navigate, login...
  });

  test('mô tả hành vi cần test', async ({ page }) => {
    // Arrange: khởi tạo page objects, data
    // Act: thực hiện hành động
    // Assert: kiểm tra kết quả
  });
});
```- Each test block must have a clear **assertion**
- Use`test.describe`to group tests by module
- Use`beforeEach` / `afterEach`for setup/teardown