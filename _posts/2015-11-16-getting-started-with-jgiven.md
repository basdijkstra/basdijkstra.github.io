---
id: 1162
title: 'Getting started with: JGiven'
date: 2015-11-16T12:43:59+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1162
permalink: /getting-started-with-jgiven/
categories:
  - Test automation tools
tags:
  - behaviour-driven development
  - java
  - jgiven
  - up and running
---
_This is the eighth article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is JGiven?**  
From the <a href="http://JGiven.org" target="_blank">JGiven.org</a> website: JGiven is a developer-friendly and pragmatic BDD tool for Java. Developers write scenarios in plain Java using a fluent, domain-specific API, JGiven generates reports that are readable by domain experts.

In short, JGiven can be used as an alternative to other BDD frameworks, such as <a href="http://www.ontestautomation.com/up-and-running-with-jbehave/" target="_blank">JBehave</a> and <a href="http://www.ontestautomation.com/writing-bdd-tests-using-selenium-and-cucumber/" target="_blank">Cucumber</a>. Where the latter two separate the features from the test code, JGiven does not and might therefore be considered more suitable to unit and integration tests rather than automated system and regression tests.

**Where can I get JGiven?**  
The JGiven sources can be downloaded from <a href="https://github.com/TNG/JGiven" target="_blank">the JGiven GitHub site</a>.

**How do I install and configure JGiven?**  
The easiest way to get started with JGiven is by creating a new Maven project and including the following dependency:

<pre class="brush: xml; gutter: false">&lt;dependency&gt;
	&lt;groupId&gt;com.tngtech.jgiven&lt;/groupId&gt;
	&lt;artifactId&gt;jgiven-testng&lt;/artifactId&gt;
	&lt;version&gt;0.9.5&lt;/version&gt;
	&lt;scope&gt;test&lt;/scope&gt;
&lt;/dependency&gt;</pre>

This is for use in combination with TestNG, which I will do in the rest of this post. If you prefer to user JUnit, use _jgiven-junit_ as your _artifactId_. Please note that this dependency does not include TestNG (or JUnit) itself, so be sure to include it separately in your _pom.xml_.

**Creating and running a first JGiven test**  
Taking my trusted ParaBank application as an example, I want to perform a test determining whether I can login successfully using a JGiven scenario. I.e., I want to execute the following scenario:

> Given I am at the login page  
> When I login as john with password demo  
> Then the login action will be successful

The implementation of this scenario as a TestNG-based test in JGiven is pretty straightforward:

<pre class="brush: java; gutter: false">public class LoginTest extends ScenarioTest&lt;GivenIAmAtTheLoginPage, WhenILoginAsJohnWithPasswordDemo, ThenTheLoginActionWillBeSuccessful&gt; {
	
	@Test
	public void aFirstLoginTest() {
		
		given().I_am_at_the_login_page();
		when().I_login_as_john_with_password_demo();
		then().the_login_action_will_be_successful();
	}
}</pre>

The _ScenarioTest_ class requires three parameters, each representing a stage in the Given-When-Then scenario. To make this compile, we also need to implement each of the three classes that are used as a parameter. As an example, this is what the implementation of the GivenIAmAtTheLoginPage class looks like:

<pre class="brush: java; gutter: false">public class GivenIAmAtTheLoginPage extends Stage&lt;GivenIAmAtTheLoginPage&gt;{
	public GivenIAmAtTheLoginPage I_am_at_the_login_page() {
		
		return self();
	}
}</pre>

The other two classes (stages) are implemented in a similar way. Now that this is done, we can run our test, which lads to the following output in the console:

<pre>Test Class: com.ontestautomation.jgiven.tests.LoginTest

 Scenario: A first login test

   Given I am at the login page
    When I login as john with password demo
    Then the login action will be successful

PASSED: aFirstLoginTest</pre>

We can see that our test passes, so we have something to build upon. No actual test actions are performed yet, so we are going to add these next. Since we are performing a Selenium WebDriver test, it&#8217;s required that we pass along the browser instance as a parameter for each of the steps. This also means we can initialize it before the test is run (using _@BeforeTest_) and destroy it afterwards (using _@AfterTest_):

<pre class="brush: java; gutter: false">public class LoginTest extends ScenarioTest&lt;GivenIAmAtTheLoginPage, WhenILoginAsJohnWithPasswordDemo, ThenTheLoginActionWillBeSuccessful&gt; {
	
	WebDriver driver;
	
	@BeforeTest
	public void initBrowser() {

		driver = new FirefoxDriver();
		driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
	}
	
	@Test
	public void aFirstLoginTest() {
		
		given().I_am_at_the_login_page(driver);
		when().I_login_as_john_with_password_demo(driver);
		then().the_login_action_will_be_successful(driver);
	}
	
	@AfterTest
	public void tearDown() {
		
		driver.quit();
	}
}</pre>

The WebDriver actions associated with the Given, When and Then steps can now be added to the respective classes as well, for example for _GivenIAmAtTheLoginPage_ this results in:

<pre class="brush: java; gutter: false">public class GivenIAmAtTheLoginPage extends Stage&lt;GivenIAmAtTheLoginPage&gt;{
	
	WebDriver _driver;
	
	public GivenIAmAtTheLoginPage I_am_at_the_login_page(WebDriver driver) {
		
		_driver = driver;
		
		_driver.get("http://parabank.parasoft.com");
		
		return self();
	}
}</pre>

If we implement the other two Stage classes in a similar vein &#8211; where the Then stage should include an actual TestNG assertion to make it a proper test case &#8211; and rerun our test, we can see that it passes again:

<pre>Test Class: com.ontestautomation.jgiven.tests.LoginTest

 Scenario: A first login test

   Given I am at the login page FirefoxDriver: firefox on WINDOWS (6bccf261-5ce0-4378-8118-b545b6c82eca)
    When I login as john with password demo FirefoxDriver: firefox on WINDOWS (6bccf261-5ce0-4378-8118-b545b6c82eca)
    Then the login action will be successful FirefoxDriver: firefox on WINDOWS (6bccf261-5ce0-4378-8118-b545b6c82eca)

PASSED: aFirstLoginTest</pre>

Note that JGiven automatically adds a bit of information on the browser instance used to the console output.

**Useful features**  
Some useful additional features of JGiven are:

  * Several types of test execution and results <a href="http://jgiven.org/docs/reportgeneration/" target="_blank">reporting</a>, including JSON and HTML
  * Support for <a href="http://jgiven.org/docs/parameterizedsteps/" target="_blank">parameterized steps</a> (in the above example, you could for instance parameterize the username and password in the When step)
  * Support for <a href="http://jgiven.org/docs/tags/" target="_blank">tags</a> to organize scenarios

**Further reading**  
Apart from the features mentioned above, JGiven provides some more useful features for creating useful BDD-style Java-based tests. A complete reference guide can be found on <a href="http://jgiven.org/docs/" target="_blank">the JGiven website</a>.

An Eclipse Maven project including the tests and test classes I&#8217;ve used in this post can be downloaded [here](http://www.ontestautomation.com/files/JGiven.zip).