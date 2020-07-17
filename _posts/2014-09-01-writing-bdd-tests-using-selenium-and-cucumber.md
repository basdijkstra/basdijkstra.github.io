---
id: 610
title: Writing BDD tests using Selenium and Cucumber
date: 2014-09-01T09:20:07+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=610
permalink: /writing-bdd-tests-using-selenium-and-cucumber/
categories:
  - Selenium
  - Test automation tools
tags:
  - behaviour-driven development
  - cucumber
  - junit
  - selenium webdriver
---
In this article, I want to introduce another Behaviour Driven Development (BDD) tool to you. It&#8217;s similar to JBehave, about which I&#8217;ve been talking in [a previous post](http://www.ontestautomation.com/up-and-running-with-jbehave/ "Up and running with: JBehave"), but with a couple of advantages:

  * It is being updated regularly, in contrast to JBehave, which has not seen any updates for quite some time now
  * It works completely separately from your Selenium WebDriver implementation, which means you can simply add it as another dependency to your existing and new Selenium projects. Again, this is unlike JBehave, which uses its own Selenium browser driver implementation, and because these aren&#8217;t updated regularly, it&#8217;s likely you&#8217;ll run into compatibility issues when trying to run Selenium + JBehave tests on newer browser versions.

**Installing and configuring Cucumber**  
The Cucumber Java implementation (Cucumber JVM) can be downloaded from the [Cucumber homepage](http://cukes.info/install-cucumber-jvm.html). Once you&#8217;ve downloaded the binaries, just add the required .jar files as dependencies to your Selenium project and you&#8217;re good to go.

**Defining the user story to be tested**  
Like with JBehave, Cucumber BDD tests are written as user stories in the Given-When-Then format. I am going to use the following login scenario as an example user story in this post:

<pre class="brush: text; gutter: false">Feature: Login action
	As a user of ParaBank
	I want to be able to log in
	So I can use the features provided
	
	Scenario: An authorized user logs in successfully
	Given the ParaBank home page is displayed
	When user "john" logs in using password "demo"
	Then the login is successful</pre>

ParaBank is a demo application from the guys at Parasoft that I use (or abuse) a lot in my demos.

In order for Cucumber to automatically detect the stories (or features, as they&#8217;re known in Cucumber), you need to make sure that they carry the _.feature_ file extension. For example, in this case, I&#8217;ve named my user story _login.feature_.

**Translating the test steps to code**  
As with JBehave, the next step is to translate the steps from the user story into runnable code. First, we are going to define the actual class to be run, which looks like this:

<pre class="brush: java; gutter: false">import org.junit.runner.RunWith;
import cucumber.api.CucumberOptions;
import cucumber.api.junit.Cucumber;

@RunWith(Cucumber.class)
@CucumberOptions(format = "pretty")
public class SeleniumCucumber {
	
}</pre>

I&#8217;ve configured my tests to run as JUnit tests, because this will make it easy to incorporate them into a CI configuration, and it auto-generates reporting in Eclipse. The _@RunWith_ annotation defines the tests to run as Cucumber tests, whereas the _@CucumberOptions_ annotation is used to set options for your tests (more info [here](http://cukes.info/api/cucumber/jvm/javadoc/cucumber/api/CucumberOptions.html)). Cucumber automatically looks for step definitions and user stories in the same package, so there&#8217;s no need for any additional configuration here. Note that in Cucumber, the class to be run cannot contain any actual implementation of the test steps. These are defined in a different class:

<pre class="brush: java; gutter: false">public class SeleniumCucumberSteps {

	WebDriver driver;
	
	@Before
	public void beforeTest() {
		driver = new FirefoxDriver();
	}

	@Given("^the ParaBank home page is displayed$")
	public void theParaBankHomepageIsDisplayed() {

		driver.get("http://parabank.parasoft.com");
	}

	@When("^user john logs in using password demo$")
	public void userJohnLogsInUsingPasswordDemo() {

		driver.findElement(By.name("username")).sendKeys("john");
		driver.findElement(By.name("password")).sendKeys("demo");
		driver.findElement(By.cssSelector("input[value=&#039;Log In&#039;]")).click();
	}

	@Then("^the login is successful$")
	public void theLoginIsSuccessful() {
		Assert.assertEquals("ParaBank | Accounts Overview",driver.getTitle());
	}
	
	@After
	public void afterTest() {
		driver.quit();
	}
}</pre>

Similar to what we&#8217;ve seen with [JBehave](http://www.ontestautomation.com/up-and-running-with-jbehave/ "Up and running with: JBehave"), each step in our user story is translated to runnable code. As the user story is quite straightforward, so is its implementation, as you can see.

I&#8217;ve used the JUnit _@Before_ and _@After_ annotations for set-up and tear-down test steps. These are steps to set up your test environment before tests are run and to restore the test environment to its original setup after test execution is finished. In our case, the only set-up step we take is the creation of a new browser instance, and the only tear-down action is closing that same browser instance. All other steps are part of the actual user story to be executed.

I&#8217;ve used the JUnit Assert feature to execute the verification from the Then part of the user story, so it shows up nicely in the reports.

That&#8217;s all there is to creating a first Cucumber test.

**Running the test**  
Now, if we run the tests we defined above by running the _SeleniumCucumber_ class, Cucumber automatically detects the user story to be executed (as it is in the same directory) and the associated step implementations (as they&#8217;re in the same package too). The result is that the user story is executed successfully and a nicely readable JUnit-format report is generated and displayed:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/09/cucumber_results1.png" alt="Cucumber test results" width="455" height="243" class="aligncenter size-full wp-image-617" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/09/cucumber_results1.png 455w, https://www.ontestautomation.com/wp-content/uploads/2014/09/cucumber_results1-300x160.png 300w" sizes="(max-width: 455px) 100vw, 455px" />](http://www.ontestautomation.com/wp-content/uploads/2014/09/cucumber_results1.png)As you can see, the test result even displays the name of the feature and the scenario that has been executed, which makes it all the more readable and usable.

**Using parameters in test steps**  
In Cucumber, we can use parameters to make our step definitions reusable. This is done by replacing the values in the step definitions with regular expressions and adding parameters to the methods that implement the steps:

<pre class="brush: java; gutter: false">@When("^user \"(.*)\" logs in using password \"(.*)\"$")
public void userJohnLogsInUsingPasswordDemo(String username, String password) {

	driver.findElement(By.name("username")).sendKeys(username);
	driver.findElement(By.name("password")).sendKeys(password);
	driver.findElement(By.cssSelector("input[value=&#039;Log In&#039;]")).click();
}</pre>

When running the tests, Cucumber will replace the regular expressions with the values defined in the user story and pass these as parameters to the method that is executed. Easy, right? You can do the same for integer, double and other parameter types just as easily if you know your way around regular expressions.

Overall, I&#8217;ve found Cucumber to be really easy to set up and use so far, even for someone who is not a true developer (I&#8217;m definitely not!). In a future post, I&#8217;ll go into more detail on adding these tests to a Continuous Integration solution.

An Eclipse project containing the example code and the user story / feature I&#8217;ve used in this post can be downloaded [here](http://www.ontestautomation.com/files/seleniumCucumber.zip).