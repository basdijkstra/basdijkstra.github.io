---
id: 350
title: Using the Page Object Design pattern in Selenium Webdriver
date: 2014-04-10T13:06:17+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=350
permalink: /using-the-page-object-design-pattern-in-selenium-webdriver/
categories:
  - Best practices
  - Selenium
tags:
  - best practice
  - page object design
  - selenium webdriver
  - single point of maintenance
---
In a previous post, we have seen how [using an object map](http://www.ontestautomation.com/building-and-using-an-object-repository-in-selenium-webdriver/ "Building and using an Object Repository in Selenium Webdriver") significantly reduces the amount of maintenance needed on your Selenium scripts when your application under test is updated. Using this object map principle minimizes duplication of code on an object level. In this post, I will introduce an additional optimization pattern that minimizes code maintenance required on a higher level of abstraction.

Even though we have successfully stored object properties in a SPOM (a Single Point Of Maintenance), we still have to write code that handles these objects every time our script processes a given page including that object in our set of test scripts. If our set of test scripts requires processing a login form five times throughout the execution, we will need to include the code that handles the objects required to log in &#8211; a username field, a password field and a submit button, for example &#8211; five times as well. If the login page changes but the objects defined previously remain the same &#8211; for example, an extra checkbox is included to have a user agree to certain terms and conditions &#8211; we still need to update our scripts five times to include the processing of the checkbox.

To eliminate this code redundancy and maintenance burden, we are going to use a different approach known as the Page Object design pattern. This pattern uses page objects that represent a web page (or a form within a page, if applicable) to separate test code (validations and test flow logic, for example) from page specific code. It does so by making all actions that can be performed on a page available as methods of the page object representing that page.

So, assuming our test scripts needs to login twice (with different credentials), instead of this code:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main(String args[]) {
	
	// start testing
	WebDriver driver = new HtmlUnitDriver();
		
	// first login
	driver.get("http://ourloginpage");
	driver.findElement(objMap.getLocator("loginUsername")).sendKeys("user1");
	driver.findElement(objMap.getLocator("loginPassword")).sendKeys("pass1");
	driver.findElement(objMap.getLocator("loginSubmitbutton")).click();
		
	// do stuff
		
	// second login
	driver.get("http://ourloginpage");
	driver.findElement(objMap.getLocator("loginUsername")).sendKeys("user2");
	driver.findElement(objMap.getLocator("loginPassword")).sendKeys("pass2");
	driver.findElement(objMap.getLocator("loginSubmitbutton")).click();
		
	// do more stuff
	
	// stop testing
	driver.close();
}</pre>

we would get

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main(String args[]) {
		
	// start testing
	WebDriver driver = new HtmlUnitDriver();
		
	// first login
	LoginPage lp = new LoginPage(driver);
	HomePage hp = lp.login("user1","pass1");
		
	// do stuff
		
	// second login
	LoginPage lp = new LoginPage(driver);
	HomePage hp = lp.login("user2","pass2");
		
	// do more stuff
		
	// stop testing
	driver.close();
}</pre>

Now, when we want to go to and handle our login page, we simply create a new instance of that page and call the _login_ method to perform our login action. This method in turn returns a HomePage object, which is a representation of the page we get after a successful login action. A sample implementation of our LoginPage object could look as follows:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public class LoginPage {
	
	private final WebDriver driver;
	
	public LoginPage(WebDriver driver) {
		this.driver = driver;
		
		if(!driver.getTitle().equals("Login page")) {
			// we are not at the login page, go there
			driver.get("http://ourloginpage");
		}
	}
	
	public HomePage login(String username, String password) {
		driver.findElement(objMap.getLocator("loginUsername")).sendKeys("username");
		driver.findElement(objMap.getLocator("loginPassword")).sendKeys("password");
		driver.findElement(objMap.getLocator("loginSubmitbutton")).click();
		return new HomePage(driver);
	}	
}</pre>

It contains a constructor that opens the login page if it is not visible already. Alternatively, you could throw an exception and stop test execution whenever the login page is not the current page, depending on how you want your test to behave. Our LoginPage class also contains a _login_ method that handles our login actions. If ever the login screen changes, we only need to update our test script once thanks to the proper use of page objects.

When the login action is completed successfully, our test returns a HomePage object. This class will be set up similar to the LoginPage class and provide methods specific to the page of our application under test it represents.

In case we also want to test an unsuccessful login, we simply add a method to our LoginPage class that executes the behaviour required:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public LoginPage incompleteLogin(String username) {
	driver.findElement(objMap.getLocator("loginUsername")).sendKeys("username");
	driver.findElement(objMap.getLocator("loginSubmitbutton")).click();
	return this;
}</pre>

This alternative login procedure does not enter a password. As a result, the user is not logged in and the login page remains visible, hence we return the current _LoginPage_ object here instead of a _HomePage_ object. If we want to test this type of incorrect login in our script, we simply call our new _incorrectLogin_ method:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main(String args[]) {
		
	// start testing
	WebDriver driver = new HtmlUnitDriver();
		
	// incorrect login
	LoginPage lp = new LoginPage(driver);
	lp = lp.incompleteLogin("user1");
	Assert.assertEquals("You forgot to type your password",lp.getError());
		
	//stop testing
	driver.quit();
}</pre>

The _getError_ method is implemented in our LoginPage class as well:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public String getError() {
	return driver.findElement(objMap.getLocator("errorField")).getText();
}</pre>

This _getError_ method is the result of another best practice. In order to keep your test code as much separated from your object code, always place your assertions outside of your page objects. If you need to validate specific values from a page, write methods that return them, as we did in the example above using the _getError_ method.

To wrap things up, using the Page Object design pattern, we introduced another Single Point of Maintenance or SPOM in our Selenium test framework. This means even less maintenance required and higher ROI achieved!

An example Eclipse project using the pattern described above can be downloaded [here](http://www.ontestautomation.com/files/seleniumPageObjectExample.zip).