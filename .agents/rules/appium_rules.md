# Rules Specific to Appium (Mobile Automation)

> Applicable when automating mobile applications with Java and Appium.

## 1. Locator Priority Order

Use a platform-specific native locator strategy (iOS / Android) instead of the web equivalent:

1.`accessibility id`— Cross-platform, most stable
2.`resource-id`(Android) — Android native attribute
3.`id` — ID chung
4. `iOS predicate string`(iOS) — Fast, iOS-specific
5.`iOS class chain`(iOS) — iOS structured query
6.`xpath`— Last option (slowest)

Correct example:```java
// Accessibility id — Cross-platform, luôn ưu tiên
driver.findElement(AppiumBy.accessibilityId("login_button"));

// Android — resource-id
driver.findElement(AppiumBy.id("com.application.xyz:id/login_button"));

// iOS — Predicate String (nhanh)
driver.findElement(AppiumBy.iOSNsPredicateString("label == 'Login'"));

// iOS — Class Chain
driver.findElement(AppiumBy.iOSClassChain(
    "**/XCUIElementTypeButton[`label == 'Login'`]"
));
```## 2. STRICTLY PROHIBITED

- Absolute XPath is position-based — any small layout change will cause failure:```java
  // NGHIÊM CẤM:
  driver.findElement(By.xpath(
      "//android.widget.FrameLayout[1]/android.widget.LinearLayout[2]/android.widget.Button[1]"
  ));
  ```- Query elements located off-screen without scrolling first
- Interact with a disabled element without checking the status
- Hardcode animation timeout

## 3. Wait Strategy

**STRICTLY PROHIBITED:**
-`Thread.sleep()`— In any case

**USE:**
- Explicit Waits with`WebDriverWait`:
  ```java
  WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(15));

  // Chờ element hiển thị
  wait.until(ExpectedConditions.visibilityOfElementLocated(
      AppiumBy.accessibilityId("welcome_text")
  ));

  // Chờ element click được
  wait.until(ExpectedConditions.elementToBeClickable(
      AppiumBy.accessibilityId("submit_button")
  ));
  ```- Handling scroll to element:```java
  // Android — UiScrollable
  driver.findElement(AppiumBy.androidUIAutomator(
      "new UiScrollable(new UiSelector().scrollable(true))" +
      ".scrollIntoView(new UiSelector().text(\"Submit\"))"
  ));
  ```## 4. Test Structure (TestNG)```java
public class LoginMobileTest extends BaseTest {

    @BeforeMethod
    public void setUp() {
        // Khởi tạo driver, capabilities...
    }

    @Test(groups = {"mobile", "regression"})
    public void testLoginSuccess() {
        // Arrange
        LoginScreen loginScreen = new LoginScreen(driver);
        String email = DataGenerator.generateEmail("loginMobile");

        // Act
        loginScreen.login(email, "ValidPass@123");

        // Assert
        HomeScreen homeScreen = new HomeScreen(driver);
        Assert.assertTrue(homeScreen.isWelcomeDisplayed(),
            "Màn hình Home phải hiển thị sau khi đăng nhập");
    }
}
```Note:
- Mobile uses **Screen Objects** (equivalent to Page Objects) — suffix`Screen`- For example:`LoginScreen.java`, `HomeScreen.java`, `SettingsScreen.java`## 5. Characteristics of Mobile Testing

- **Screen rotation:** Test both portrait and landscape if the app supports:```java
  driver.rotate(ScreenOrientation.LANDSCAPE);
  ```- **Background/Foreground:** Test app when switching to background and then back:```java
  driver.runAppInBackground(Duration.ofSeconds(5));
  ```- **Push Notification:** Verify notification using Appium notification listener
- **Permission Dialog:** Handle dialog asking for permission (camera, location...):```java
  // Android — Auto-grant permissions trong capabilities
  capabilities.setCapability("autoGrantPermissions", true);
  ```
