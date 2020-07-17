---
id: 688
title: Using the Page Object Model pattern in Selenium + TestNG tests
date: 2014-12-30T22:39:23+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=688
permalink: /using-the-page-object-model-pattern-in-selenium-testng-tests/
categories:
  - Best practices
  - Selenium
tags:
  - maintainability
  - page object design
  - selenium webdriver
  - testng
---
After having introduced the Selenium + TestNG combination in [my previous post](http://www.ontestautomation.com/up-and-running-with-testng/ "Up and running with: TestNG"), I would like to show you how to apply the [Page Object Model](http://www.ontestautomation.com/using-the-page-object-design-pattern-in-selenium-webdriver/ "Using the Page Object Design pattern in Selenium Webdriver"), an often used method for improving maintainability of Selenium tests, to this setup. To do so, we need to accomplish the following steps:

  * Create Page Objects representing pages of a web application that we want to test
  * Create methods for these Page Objects that represent actions we want to perform within the pages that they represent
  * Create tests that perform these actions in the required order and performs checks that make up the test scenario
  * Run the tests as TestNG tests and inspect the results

**Creating Page Objects for our test application**  
For this purpose, again I use the ParaBank demo application that can be found [here](http://parabank.parasoft.com). I&#8217;ve narrowed the scope of my tests down to just three of the pages in this application: the login page, the home page (where you end up after a successful login) and an error page (where you land after a failed login attempt). As an example, this is the code for the login page:

<pre class="brush: java; gutter: false">package com.ontestautomation.seleniumtestngpom.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage {
	
	private WebDriver driver;
	
	public LoginPage(WebDriver driver) {
		
		this.driver = driver;
		
		if(!driver.getTitle().equals("ParaBank | Welcome | Online Banking")) {
			driver.get("http://parabank.parasoft.com");
		}		
	}
	
	public ErrorPage incorrectLogin(String username, String password) {
		
		driver.findElement(By.name("username")).sendKeys(username);
		driver.findElement(By.name("password")).sendKeys(password);
		driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();
		return new ErrorPage(driver);
	}
	
	public HomePage correctLogin(String username, String password) {
		
		driver.findElement(By.name("username")).sendKeys(username);
		driver.findElement(By.name("password")).sendKeys(password);
		driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();
		return new HomePage(driver);
	}
}</pre>

It contains a constructor that returns a new instance of the _LoginPage_ object as well as two methods that we can use in our tests: _incorrectLogin_, which sends us to the error page and _correctLogin_, which sends us to the home page. Likewise, I&#8217;ve constructed Page Objects for these two pages as well. A link to those implementations can be found at the end of this post.

Note that this code snippet isn&#8217;t optimized for maintainability &#8211; I used direct references to element properties rather than some sort of element-level abstraction, such as an [Object Repository](http://www.ontestautomation.com/building-and-using-an-object-repository-in-selenium-webdriver/ "Building and using an Object Repository in Selenium Webdriver").

**Creating methods that perform actions on the Page Objects**  
You&#8217;ve seen these for the login page in the code sample above. I&#8217;ve included similar methods for the other two pages. A good example can be seen in the implementation of the error page Page Object:

<pre class="brush: java; gutter: false">package com.ontestautomation.seleniumtestngpom.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class ErrorPage {
	
	private WebDriver driver;
	
	public ErrorPage(WebDriver driver) {
		
		this.driver = driver;
	}
	
	public String getErrorText() {
		
		return driver.findElement(By.className("error")).getText();
	}
}</pre>

By implementing a _getErrorText_ method to retrieve the error message that is displayed on the error page, we can call this method in our actual test script. It is considered good practice to separate the implementation of your Page Objects from the actual assertions that are performed in your test script (separation of responsibilities). If you need to perform additional checks, just add a method that returns the actual value displayed on the screen to the associated page object and add assertions to the scripts where this check needs to be performed.

**Create tests that perform the required actions and execute the required checks**  
Now that we have created both the page objects and the methods that we want to use for the checks in our test scripts, it&#8217;s time to create these test scripts. This is again pretty straightforward, as this example shows (imports removed for brevity):

<pre class="brush: java; gutter: false">package com.ontestautomation.seleniumtestngpom.tests;

public class TestNGPOM {
	
	WebDriver driver;
	
	@BeforeSuite
	public void setUp() {
		
		driver = new FirefoxDriver();
		driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
	}
	
	@Parameters({"username","incorrectpassword"})
	@Test(description="Performs an unsuccessful login and checks the resulting error message")
	public void testLoginNOK(String username, String incorrectpassword) {
		
		LoginPage lp = new LoginPage(driver);
		ErrorPage ep = lp.incorrectLogin(username, incorrectpassword);
		Assert.assertEquals(ep.getErrorText(), "The username and password could not be verified.");
	}
	
	@AfterSuite
	public void tearDown() {
		
		driver.quit();
	}
}</pre>

Note the use of the page objects and the check being performed using methods in these page object implementations &#8211; in this case the _getErrorText_ method in the error page page object.

As we have designed our tests as Selenium + TestNG tests, we also need to define a _testng.xml_ file that defines which tests we need to run and what parameter values the parameterized _testLoginOK_ test takes. Again, see [my previous post](http://www.ontestautomation.com/up-and-running-with-testng/ "Up and running with: TestNG") for more details.

<pre class="brush: xml; gutter: false">&lt;!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" &gt;
 
&lt;suite name="My first TestNG test suite" verbose="1" &gt;
  &lt;parameter name="username" value="john"/&gt;
  &lt;parameter name="password" value="demo"/&gt;
  &lt;test name="Login tests"&gt;
    &lt;packages&gt;
      &lt;package name="com.ontestautomation.seleniumtestngpom.tests" /&gt;
   &lt;/packages&gt;
 &lt;/test&gt;
&lt;/suite&gt;</pre>

**Run the tests as TestNG tests and inspect the results**  
Finally, we can run our tests again by right-clicking on the _testng.xml_ file in the Package Explorer and selecting &#8216;Run As > TestNG Suite&#8217;. After test execution has finished, the test results will appear in the &#8216;Results of running suite&#8217; tab in Eclipse. Again, please note that using meaningful names for tests and test suites in the _testng.xml_ file make these results much easier to read and interpret.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_eclipse.png" alt="TestNG test results in Eclipse" width="511" height="194" class="aligncenter size-full wp-image-698" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_eclipse.png 511w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_eclipse-300x114.png 300w" sizes="(max-width: 511px) 100vw, 511px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_eclipse.png)

An extended HTML report can be found in the test-output subdirectory of your project:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_html.png" alt="TestNG HTML test results" width="1512" height="470" class="aligncenter size-full wp-image-699" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_html.png 1512w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_html-300x93.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_html-1024x318.png 1024w" sizes="(max-width: 1512px) 100vw, 1512px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_pom_results_html.png)

The Eclipse project I have used for the example described in this post, including a sample HTML report as generated by TestNG, can be downloaded [here](http://www.ontestautomation.com/files/SeleniumTestNGPOM.zip).