---
name: e2e-testing
description: Implement end-to-end testing with Selenium, Playwright for CIA platform UI and workflow validation Use when this capability is needed.
metadata:
  author: hack23
---

# E2E Testing Skill

## Purpose
Test complete user workflows and UI interactions using Selenium/Playwright.

## When to Use
- ✅ Testing user registration/login flows
- ✅ Testing complex multi-step workflows
- ✅ Testing UI rendering and interactions
- ✅ Cross-browser compatibility testing

## Selenium WebDriver Pattern
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = ApplicationContext.class)
public class LoginE2ETest {
    private WebDriver driver;
    
    @Before
    public void setup() {
        driver = new ChromeDriver();
    }
    
    @Test
    public void shouldLoginSuccessfully() {
        driver.get("http://localhost:8080/login");
        driver.findElement(By.id("username")).sendKeys("testuser");
        driver.findElement(By.id("password")).sendKeys("password");
        driver.findElement(By.id("login-btn")).click();
        
        assertThat(driver.getCurrentUrl()).contains("/dashboard");
    }
}
```

## Page Object Model
```java
public class LoginPage {
    private WebDriver driver;
    
    @FindBy(id = "username")
    private WebElement usernameField;
    
    @FindBy(id = "password")
    private WebElement passwordField;
    
    @FindBy(id = "login-btn")
    private WebElement loginButton;
    
    public void login(String username, String password) {
        usernameField.sendKeys(username);
        passwordField.sendKeys(password);
        loginButton.click();
    }
}
```

## References
- Selenium: https://www.selenium.dev/
- E2ETestPlan.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
