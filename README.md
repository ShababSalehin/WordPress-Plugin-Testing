
# WordPress PlugIn testing

WordPress plugins are designed to enhance the functionality and performance of your WordPress website. We can run an automation test to see plug-in installation,  activation, and configuration of the plugin's settings to customize the appearance and behavior of the plug-in to suit your website's design and preferences.  

### Plugin info: https://wordpress.org/plugins/wp-dark-mode/  

## Target/goals
following scenarios, which will be replicated  
1. Log in to your WordPress site.  
2. Check whether the “WP Dark Mode” Plugin is Active or not.  
3. If Active, navigate to the WP Dark Mode & continue. Otherwise, Install the Plugin and Activate it.  
4. Enable Backend Darkmode from Settings -> General Settings.  
5. Validate whether the dark mode is working or not on the Admin Dashboard.  
6. Navigate to the WP Dark Mode.  
7. From Settings -> Switch Settings - Change the “Floating Switch Style” from the default selections (Select any one from the available options, except the default selected one).  
8. From Settings -> Switch Settings - Select Custom Switch size & Scale it to 220.
9. From Settings -> Switch Settings - Change the Floating Switch Position (Left Bottom).  
10. Disable Keyboard Shortcut from the Accessibility Settings.  
11. From Settings -> Animation - Enable “Darkmode Toggle Animation” & change the “Animation Effect” from the default selections (Select any one from the available options, except the default selected one).  
12.  Validate whether the Darkmode is working or not from the Frontend.  

## Prerequisites
Install Xampp: https://www.apachefriends.org/download.html  

Open Xampp to start Apache and MySQL  

Download Wordpress file: https://wordpress.org/download/  

Create the database(watch to the .env.example file):   

My file directory  
[DB_HOST = localhost  
DB_PORT = 3306  
DB_DATABASE = assignmentwp  
DB_USERNAME =root  
DB_PASSWORD= ]  



## Test workflow

### Test file information  
The following instructions will help you navigate those testing pages. We will create some packages. At the package level, there is a list of classes where you can create methods, use methods for particular pages, and run and test the testing pages separately  

1. Set Environment   
i) pom.xml [dependencies set]  
ii) BrowserSetup[create separate package ]  

2. Page Object Model: create methods, using methods for separate page and create test cases of those pages  
i) Methods[package name:TestingMethodsAndPages]  
ii) Page objects[package name:TestingMethodsAndPages]  
iii) TestCases [package name: Testpages ]  

3. Create Allure report   
i) pom.xml [dependencies set for allure report]  
ii) Testng.xml [to run all test file togather]  


##  Set Environment
Create a Maven Project
Set pom.xml file 
pom.xml file Code:
Set Under Dependencies
```ruby
  <dependencies>
      <!-- https://mvnrepository.com/artifact/org.testng/testng -->
<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng</artifactId>
    <version>7.6.1</version>
    <scope>test</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/org.seleniumhq.selenium/selenium-java -->
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>4.6.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.github.bonigarcia/webdrivermanager -->
<dependency>
    <groupId>io.github.bonigarcia</groupId>
    <artifactId>webdrivermanager</artifactId>
    <version>5.3.0</version>
</dependency>
  </dependencies>
</project>
```

Create Some class under Packages 
One of the package will hold BorwserSetup in which we run the automation Code

Inside BorwserSetup Class:  

It will hold three Driver(Chrome, Firefox, and Edge), use according to your preferences 
I prefer to use the Edge Driver (Edge Browser) to run my code.
```ruby
package Wppool.AssignmentWP;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.edge.EdgeOptions;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxOptions;
import org.testng.annotations.AfterSuite;
import org.testng.annotations.BeforeSuite;
import io.github.bonigarcia.wdm.WebDriverManager;

public class BrowserSetup {
	private static String BrowserName = System.getProperty("browser", "Edge");
	private static final ThreadLocal<WebDriver> DRIVER_LOCAL = new ThreadLocal<>();
	public static WebDriver getDriver() {
		return DRIVER_LOCAL.get();
	}
	public static void setDriver(WebDriver driver) {
		BrowserSetup.DRIVER_LOCAL.set(driver);
	}
	public static WebDriver getBrowser(String BrowserName) {
		switch (BrowserName.toLowerCase()) {
		case "chrome":
			ChromeOptions option1 = new ChromeOptions();
			option1.addArguments("--remote-allow-origins=*");
			WebDriverManager.chromedriver().setup();
			return new ChromeDriver(option1);
		case "edge":
			EdgeOptions option2 = new EdgeOptions();
			option2.addArguments("--remote-allow-origins=*");
			WebDriverManager.edgedriver().setup();
			return new EdgeDriver(option2);
		case "firefox":
			FirefoxOptions option3 = new FirefoxOptions();
			option3.addArguments("--remote-allow-origins=*");
			WebDriverManager.firefoxdriver().setup();
			return new FirefoxDriver(option3);
		default:
			throw new RuntimeException("Browser Not found");
		}
	}
	@BeforeSuite
	public static synchronized void setBrowser() {
		WebDriver webDriver = getBrowser(BrowserName);
		webDriver.manage().window().maximize();
		setDriver(webDriver);
	}
	@AfterSuite
	public static synchronized void quitBrowser() {
		getDriver().quit();
	}
}

```

## Page Object model
### Methods
Create a package which include classes like method and other testing Page

method class includes methods of getElement, click, hover, field fillup, visible wait element, click wait element and take screenshot for allure report.
This class appears to contain various methods for interacting with web elements using Selenium WebDriver. Here's a brief overview of the methods:  
* getElement(By locator): Returns a WebElement located by the given By locator.  
* clickElement(By locator): Clicks on the WebElement identified by the provided locator.  
* FieldValue(By locator, String text): Enters the specified text into the input field identified by the given locator.  
* WaitElementVisible(By locator): Waits for the visibility of the element located by the provided locator with a timeout of 20 seconds.  
* Hover(By locator): Performs a hover action on the element located by the given locator using the Actions class.  
* DropDownSelectElement(By locator): Selects the first option in the dropdown list identified by the given locator.  
* takeScreenshot(String name): Captures a screenshot and attaches it to the Allure report with the specified name.  

package TestingMethodsAndPages;

import static Wppool.AssignmentWP.BrowserSetup.getDriver;
import java.io.ByteArrayInputStream;
import java.time.Duration;
import org.openqa.selenium.By;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.interactions.Actions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.Select;
import org.openqa.selenium.support.ui.WebDriverWait;
import io.qameta.allure.Allure;

public class Methods {
	public WebElement getElement(By locator) {
		//import static Wppool.AssignmentWP.BrowserSetup.getDriver;
		return getDriver().findElement(locator//driver = getDriver(
	}
	public void clickElement(By locator) {
		getElement(locator).click(
	}
	public void FieldValue(By locator, String text) {
		getElement(locator).sendKeys(text
	}
	public void WaitElementVisible(By locator) {
		WebDriverWait wait = new WebDriverWait(getDriver(), Duration.ofSeconds(20)
		wait.until(ExpectedConditions.visibilityOfElementLocated(locator)
	}
	public void Hover(By locator) {
		Actions action = new Actions(getDriver()
		//action.moveToElement(driver.findElement(locator)).perform(
		action.moveToElement(getElement(locator)).perform(;
	}
	public void DropDownSelectElement(By locator) {
		Select	select = new Select(getElement(locator)
		select.selectByIndex(0
	}
	//for allure report
		public void takeScreenshot(String name) {
			Allure.addAttachment(name, new ByteArrayInputStream(((TakesScreenshot)getDriver()).getScreenshotAs(OutputType.BYTES))
		}
}
```
### Scenario Replication Classes
Login to Dashbord  
1. Log in to your WordPress site.  
2. Check whether the “WP Dark Mode” Plugin is Active or not.  
Login to Dashbord page objects and there intented xpaths.



This class  represent a set of methods related to the WordPress login portal. Here's a brief overview of the class:  
Element Locators:  
USERNAME: Locator for the username input field.  
PASSWORD: Locator for the password input field.  
LOGINBUTTON: Locator for the login button.  
DASHBORD: Locator for the Dashboard heading.  
LOGOUT: Locator for the logout link.  
Method: WordPressLogin()  
Attempts to log in to WordPress with a predefined username and password.
Calls methods from the Methods class, such as FieldValue, clickElement, and WaitElementVisible. 
Login to Dashbord objects and methods
Login to Dashbord testSuite

#### Replication of three, four, and five scenarios(3,4, and 5):   
3. If Active, navigate to the WP Dark Mode & continue. Otherwise, Install the Plugin and Activate it.  
4. Enable Backend Darkmode from Settings -> General Settings.  
5. Validate whether the Darkmode is working or not on the Admin Dashboard.  

After login to dashbord go to plugin option in the menu bar and hover to wait add plugins option is visible, then click it and wait for track the Add plugins page visible then fill up search field to search WP Dark Mode when the desire plug in option visible then install it and wait for activation button appears and the activate it.  

This class appears to represent a set of methods related to setting up Dark Mode settings in a WordPress environment. Here's a brief overview of the class:

* Element Locators:  
DARKMODESETTINGSMENU: Locator for the WP Dark Mode settings menu.  
WaitDMsettingsVisible: Locator for the "Settings" link to wait for visibility.  
WaitSettingPage: Locator for the heading on the WP Dark Mode Settings page.  
EnableBackendDarkMode: Locator for enabling backend dark mode.  
DASHBOARD: Locator for the Dashboard link.  
WaitDashbord: Locator for waiting for the Dashboard heading to be visible.  
VALIDATEMODEBUTTON: Locator for a button to validate the dark mode.  
Method: setUpSetting()  
* Performs a series of actions to set up Dark Mode settings:  
Hovers over the Dark Mode settings menu.  
Waits for the "Settings" link to be visible.  
Clicks on the "Settings" link.  
Waits for the WP Dark Mode Settings page to be visible.  
Clicks on the button to enable backend dark mode.  
Clicks on the Dashboard link.  
Waits for the Dashboard heading to be visible.  
Clicks on the button to validate the dark mode.  
Takes screenshots at two different points in the process.  
