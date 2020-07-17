---
id: 1216
title: Using the LoadableComponent pattern for better Page Object handling in Selenium
date: 2016-01-26T15:08:41+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1216
permalink: /using-the-loadablecomponent-pattern-for-better-page-object-handling-in-selenium/
categories:
  - Selenium
tags:
  - java
  - loadablecomponent
  - page object design
  - selenium webdriver
---
I&#8217;m sure that by now, everybody who is at least remotely involved in the creation of automated user interface tests using Selenium WebDriver is aware of the Page Object pattern (read more about it <a href="https://code.google.com/p/selenium/wiki/LoadableComponent" target="_blank">here</a> and <a href="http://www.ontestautomation.com/using-the-page-object-design-pattern-in-selenium-webdriver/" target="_blank">here</a>) for introducing modularity and reusability in their automated tests. In this post I will introduce the LoadableComponent pattern, an extension to this pattern that standardizes handling the loading and verifying loading status of these page objects.

**Why use the LoadableComponent pattern?**  
Out of the box, Selenium WebDriver does a good job of determining whether an HTML page has been loaded by using the document.readyState property, which is part of the <a href="https://w3c.github.io/webdriver/webdriver-spec.html" target="_blank">W3C WebDriver specification</a>. However, this property alone is not always enough to assert that all dynamic content on your page has been fully loaded and that all elements required in your test script are present on the page. This is especially the case when your web page uses a JavaScript-heavy framework such as the popular <a href="https://angularjs.org/" target="_blank">AngularJS</a> or <a href="http://knockoutjs.com/" target="_blank">KnockoutJS</a> frameworks.

So, even when Selenium tells you the page is loaded and continues to execute the next step in your test, this step might fail because the actual element needed for that step is not yet visible, clickable or otherwise ready. You can solve this problem to an extent by using suitable <a href="http://www.ontestautomation.com/using-wrapper-methods-for-better-error-handling-in-selenium/" target="_blank">wrapper methods</a> for the standard Selenium API methods, but wouldn&#8217;t it be nice to enhance our Page Objects with a generic approach to evaluate page load status? This is where the LoadableComponent pattern comes in.

**Introducing the LoadableComponent pattern**  
To explain the concept of the LoadableComponent pattern, we will again turn to the <a href="http://parabank.parasoft.com" target="_blank">ParaBank application</a>. _LoadableComponent_ is a base class in Selenium, which means that you can simply define your Page Objects as an extension of the _LoadableComponent_ class. So, for example, we can simply define a _LoginPage_ object as follows:

<pre class="brush: java; gutter: false">public class LoginPage extends LoadableComponent&lt;LoginPage&gt; {
  // class implementation will be explained later 
}</pre>

This does nothing more than defining this class as a _LoadableComponent_ that loads the LoginPage page.

Now, by having our Page Objects extend the _LoadableComponent_ base class, we need to implement two new methods, _load()_ and _isLoaded()_ (note that in C#, these are called _ExecuteLoad()_ and _EvaluateLoadedStatus()_ for some reason). These methods provide the added value of using the LoadableComponent pattern. The _load()_ method contains the code that is executed to navigate to the page, while the _isLoaded()_ method is used to evaluate whether we are on the correct page and whether page loading has finished successfully. Using _LoadableComponent_, our _LoginPage_ class now looks like this:

<pre class="brush: java; gutter: false">public class LoginPage extends LoadableComponent&lt;LoginPage&gt; {
	
	private WebDriver driver;
	
	public LoginPage(WebDriver driver) {
		
		this.driver = driver;
		driver.get("http://parabank.parasoft.com");
	}
	
	@Override
	protected void isLoaded() throws Error {
		
		if(!PageLoad.myElementIsClickable(this.driver, By.name("username"))) {
			throw new Error("LoginPage was not successfully loaded");
		}
	}

	@Override
	protected void load() {		
	}
}</pre>

Note the use of a custom wrapper boolean method _myElementIsClickable()_ to determine whether the page has loaded successfully. It can be used as a generic way to specify which element must be present for any given Page Object in order to consider it fully loaded:

<pre class="brush: java; gutter: false">public static boolean myElementIsClickable (WebDriver driver, By by) {
		
	try
	{
		new WebDriverWait(driver, 10).until(ExpectedConditions.elementToBeClickable(by));
	}
	catch (WebDriverException ex)
	{
		return false;
	}
	return true;		
}</pre>

Also, for most pages I don&#8217;t actually put code inside the body of the _load()_ method. This is because in a typical application, most Page Objects are only accessed by means of navigation via other Page Objects instead of being accessed directly. Even for the LoginPage I have included the _navigate()_ call in the constructor, because in this way we do not need to wait for the timeout in _isLoaded()_ to be exceeded before the _navigate()_ method is called from within _load()_. The example project I will link to at the end of this post shows you how I implemented the LoadableComponent pattern in my latest project.

So, now that we&#8217;ve implemented our Page Objects as LoadableComponents, we can use it in our tests simply by doing this:

<pre class="brush: java; gutter: false">new LoginPage(driver).get();</pre>

The _get()_ method is implemented as follows:

<pre class="brush: java; gutter: false">public T get() {
	try {
		isLoaded();
		return (T) this;
	} catch (Error e) {
		load();
	}
 
	isLoaded();
 
	return (T) this;
}</pre>

In plain English: it calls _isLoaded()_ first to see whether the page is loaded. If this is not the case, it calls _load()_ to load the page. Afterwards, it calls _isLoaded()_ again to see if the page is now successfully loaded.

**Leveraging LoadableComponents in your tests**  
To ensure in your tests that your Page Objects are loaded before moving on, you can simply call _get()_ each time a Page Object is instantiated:

<pre class="brush: java; gutter: false">@Test
public void validLoginTest() {

	// Load login page
	LoginPage loginPage = new LoginPage(driver).get();

	// Log in using valid credentials
	HomePage homePage = loginPage.correctLogin("john", "demo").get();

	// Load home page and check welcome text
	Assert.assertEquals("Welcome text is correct","Welcome John Smith", homePage.getWelcomeString());
}</pre>

The assertion in _isLoaded()_ will fail if _MyElementIsClickable()_ returns _false_, leading to the test as a whole to fail:

<a href="http://www.ontestautomation.com/using-the-loadablecomponent-pattern-for-better-page-object-handling-in-selenium/loadablecomponent_error/" rel="attachment wp-att-1230"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/01/loadablecomponent_error.png" alt="LoadableComponent error" width="1281" height="294" class="aligncenter size-full wp-image-1230" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/01/loadablecomponent_error.png 1281w, https://www.ontestautomation.com/wp-content/uploads/2016/01/loadablecomponent_error-300x69.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/01/loadablecomponent_error-768x176.png 768w, https://www.ontestautomation.com/wp-content/uploads/2016/01/loadablecomponent_error-1024x235.png 1024w" sizes="(max-width: 1281px) 100vw, 1281px" /></a>

**Enhancing the LoadableComponent class**  
Finally, another interesting use of the LoadableComponent pattern is writing your own implementation of the class. In that way you can, for example, generate specific logging to the console or to an <a href="http://extentreports.relevantcodes.com/java/version2/docs.html" target="_blank">ExtentReports</a> report in case you use it.

The source code for the original LoadableComponent implementation can be found <a href="https://code.google.com/p/selenium/source/browse/java/client/src/org/openqa/selenium/support/ui/LoadableComponent.java" target="_blank">here</a>. A simple addition is writing the error message that is thrown by _isLoaded()_ (which in turn is the error message thrown by the performed assertion) to the console:

<pre class="brush: java; gutter: false">public abstract class CustomLoadableComponent&lt;T extends CustomLoadableComponent&lt;T&gt;&gt; {

	@SuppressWarnings("unchecked")
	public T get() {
		try {
			isLoaded();
			return (T) this;
		} catch (Error e) {
			// This is the extra line of code 
			System.out.println("Error encountered during page load: " + e.getMessage());
			load();
		}

		isLoaded();

		return (T) this;
	}

	protected abstract void load();

	protected abstract void isLoaded() throws Error;
}</pre>

Now you can simply define your Page Objects as an extension of the _CustomLoadableComponent_ class:

<pre class="brush: java; gutter: false">public class LoginPage extends CustomLoadableComponent&lt;LoginPage&gt; {
  // class implementation remains the same 
}</pre>

When we run our tests and encounter an error, we see that the error message is written to the console:  
<a href="http://www.ontestautomation.com/using-the-loadablecomponent-pattern-for-better-page-object-handling-in-selenium/custom_loadablecomponent_output/" rel="attachment wp-att-1231"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/01/custom_loadablecomponent_output.png" alt="Custom LoadableComponent output" width="771" height="285" class="aligncenter size-full wp-image-1231" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/01/custom_loadablecomponent_output.png 771w, https://www.ontestautomation.com/wp-content/uploads/2016/01/custom_loadablecomponent_output-300x111.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/01/custom_loadablecomponent_output-768x284.png 768w" sizes="(max-width: 771px) 100vw, 771px" /></a>

A working Eclipse project (created using Maven) containing all of the code samples included in this post can be downloaded <a href="http://www.ontestautomation.com/files/SeleniumLoadableComponent.zip" target="_blank">here</a>. Just run the tests in the _tests_ package to see for yourself how it works.