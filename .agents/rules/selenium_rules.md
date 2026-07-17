# Selenium WebDriver Specific Rules

> Applicable when automating the browser with Java and Selenium WebDriver.

## 1. Locator Priority Order

Strictly follow the following order to ensure speed and stability:

1.`id`— Fastest, most unique
2.`data-testid` / `data-test` / `data-qa`— Attributes specialized for testing
3.`name`— Standard HTML attributes
4.`cssSelector`— Flexible, fast
5.`xpath`— Final choice

Correct example:```java
driver.findElement(By.id("login-btn"));
driver.findElement(By.cssSelector("button[data-testid='submit-btn']"));
driver.findElement(By.name("username"));
```Wrong example — position-dependent structural XPath:```java
// NGHIÊM CẤM: XPath tuyệt đối dựa trên DOM structure
driver.findElement(By.xpath("//div[3]/div[2]/form/div[1]/button"));
```## 2. Wait Strategy

**STRICTLY PROHIBITED:**
-`Thread.sleep()`— In any case
- Any way fixed timeout

**USE:**
- Java Explicit Waits with`WebDriverWait` + `ExpectedConditions`:

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Chờ element hiển thị
WebElement element = wait.until(
    ExpectedConditions.visibilityOfElementLocated(By.id("profile"))
);

// Chờ element click được
wait.until(ExpectedConditions.elementToBeClickable(By.id("submit-btn")));

// Chờ text xuất hiện
wait.until(ExpectedConditions.textToBePresentInElementLocated(
    By.id("message"), "Thành công"
));

// Chờ URL chuyển hướng
wait.until(ExpectedConditions.urlContains("/dashboard"));
```- Can create custom`FluentWait`If you need flexible polling:```java
Wait<WebDriver> fluentWait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(15))
    .pollingEvery(Duration.ofMillis(500))
    .ignoring(NoSuchElementException.class);
```## 3. Browser Settings

- **Viewport:** Set desktop viewport (`1920x1080`) khi debug:
  ```java
  driver.manage().window().setSize(new Dimension(1920, 1080));
  ```- **Headed mode:** Required when debugging (not set`--headless`)
- **Headless mode:** Only used when testing has been PASS on the head or in CI/CD

## 4. Test Structure (TestNG)```java
public class LoginTest extends BaseTest {

    @BeforeMethod
    public void setUp() {
        // Navigate, setup data...
    }

    @Test(groups = {"smoke", "regression"})
    public void testLoginWithValidCredentials() {
        // Arrange
        LoginPage loginPage = new LoginPage(driver);
        String email = DataGenerator.generateEmail("login");

        // Act
        loginPage.login(email, "ValidPass@123");

        // Assert
        DashboardPage dashboard = new DashboardPage(driver);
        Assert.assertTrue(dashboard.isDisplayed(),
            "Dashboard phải hiển thị sau khi đăng nhập thành công");
    }

    @AfterMethod
    public void tearDown() {
        // Cleanup...
    }
}
```## 5. Assertions (Check Results)

- Use TestNG Assertions (`Assert.assertEquals`, `Assert.assertTrue`...)
- Always add **message description** to assertion:```java
  Assert.assertEquals(actualTitle, "Dashboard", "Tiêu đề trang phải là Dashboard");
  Assert.assertTrue(element.isDisplayed(), "Element phải hiển thị trên trang");
  ```- Each test method must have at least **1 assertion** at the end