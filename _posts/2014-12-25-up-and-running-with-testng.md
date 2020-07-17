---
id: 676
title: 'Up and running with: TestNG'
date: 2014-12-25T13:42:57+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=676
permalink: /up-and-running-with-testng/
categories:
  - Test automation tools
tags:
  - java
  - test automation
  - testng
  - up and running
---
_This is the fifth article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is TestNG?**  
From the [TestNG.org](http://testng.org) website: TestNG is a testing framework inspired from JUnit and NUnit but introducing some new functionalities that make it more powerful and easier to use, such as annotations and support for data driven testing.

**Where can I get TestNG?**  
TestNG can be downloaded from [this site](http://testng.org). For Eclipse users, it is highly recommended to install the TestNG plugin for Eclipse for maximum ease of use. IDEA IntelliJ supports TestNG natively as of version 7.

**How do I install and configure TestNG?**  
Since TestNG is supported natively from IntelliJ 7 onwards, there&#8217;s no need for additional configuration for IntelliJ users. When you install the TestNG for Eclipse plugin as described, you&#8217;re set to create your first TestNG test as well, as can be read [here](http://testng.org/doc/eclipse.html). In other situations, you can download the .jar files from [here](http://testng.org/doc/download.html) as well.

**Creating a first TestNG test**  
As I have done in the past, I&#8217;ll (ab)use the ParaBank demo application at the Parasoft website for our first TestNG test. I&#8217;ll use Selenium WebDriver to perform the test steps, and will use TestNG to perform the checks that we want to do and the reporting. Let&#8217;s say we want to verify that we can login successfully to the ParaBank application, given the right credentials. A Selenium + TestNG test that performs this test looks like this (import statements removed for brevity):

<pre class="brush: java; gutter: false">public class ParabankTestNG {
	
	WebDriver driver;
	
	@BeforeSuite
	public void setUp() {
		
		driver = new FirefoxDriver();
		driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
	}
	
	@Test(description="Tests a successful login")
	public void testLoginOK() {
		
		driver.get("http://parabank.parasoft.com");
		driver.findElement(By.name("username")).sendKeys("john");
		driver.findElement(By.name("password")).sendKeys("demo");
		driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();
		Assert.assertEquals("ParaBank | Accounts Overview",driver.getTitle());
		driver.findElement(By.partialLinkText("Log Out")).click();
	}
	
	@AfterSuite
	public void tearDown() {
		
		driver.quit();
	}
}</pre>

Note that this test looks almost identical to a Selenium + JUnit test. The only difference is the use of the _@BeforeSuite_ and _@AfterSuite_ annotations for test setup and teardown, where we would use _@Before_ and _@After_ in JUnit. TestNG uses a large variety of [annotations](http://testng.org/doc/documentation-main.html#annotations) that can be used to enhance your tests and test suites.

**Running the test**  
Again, as I&#8217;m an Eclipse user, I&#8217;ll show you how to execute your tests in Eclipse only. Please refer to the [TestNG website](http://testng.org/doc/index.html) for instructions for other IDEs.

For those that installed the TestNG plugin for Eclipse, there are two simple ways to start a TestNG test. First, we can simply right-click our test .java file in the Package Explorer and select &#8216;Run As > TestNG Test&#8217;. This is perfectly suitable when you have a single class containing all of your tests.

However, for larger projects, this will typically not be the case. For those situations, we can create an XML file called _testng.xml_ that contains instructions on which tests to run and how they should be run. You can find instructions on the use of _testng.xml_ files [here](http://testng.org/doc/documentation-main.html#testng-xml). As an example, we can run all TestNG tests in a specific package using the following instructions:

<pre class="brush: xml; gutter: false">&lt;!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" &gt;
 
&lt;suite name="My first TestNG test suite" verbose="1" &gt;
  &lt;test name="Login tests"   &gt;
    &lt;packages&gt;
      &lt;package name="com.ontestautomation.selenium.testng" /&gt;
   &lt;/packages&gt;
 &lt;/test&gt;
&lt;/suite&gt;</pre>

The _verbose_ attribute specifies the verbosity of information logged to the console, where 1 is low and 10 is high.

Running a test using the _testng.xml_ file can be done just as easily by right-clicking on it in the Package Explorer and selecting &#8216;Run As > TestNG Suite&#8217;. Test results can be reviewed in the &#8216;Results of running suite&#8217; tab in Eclipse. Note that using meaningful names for tests and test suites in the _testng.xml_ file make these results much easier to read and interpret:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_eclipse.png" alt="TestNG test results in Eclipse" width="1436" height="286" class="aligncenter size-full wp-image-683" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_eclipse.png 1436w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_eclipse-300x60.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_eclipse-1024x204.png 1024w" sizes="(max-width: 1436px) 100vw, 1436px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_eclipse.png)

Using the _testng.xml_ file also makes it easy to specify exactly which tests are run in a specific test suite, and also in which order they are executed. By default, the order in which the tests appear in the _testng.xml_ file defines the order in which the tests are run.

**Useful features**  
one very useful feature of TestNG is the ability to easily parameterize tests from _testng.xml_. For example, if we want to parameterize the input values from the login test above, we first redefine the method in the test class to take parameters:

<pre class="brush: java; gutter: false">@Parameters({"username","password"})
@Test(description="Tests a successful login")
public void testLoginOK(String username, String password) {
		
	driver.get("http://parabank.parasoft.com");
	driver.findElement(By.name("username")).sendKeys(username);
	driver.findElement(By.name("password")).sendKeys(password);
	driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();
	Assert.assertEquals("ParaBank | Accounts Overview",driver.getTitle());
	driver.findElement(By.partialLinkText("Log Out")).click();
}</pre>

Note the use of the _@Parameter_ annotation to link the arguments of our test method to the parameters you define in the _testng.xml_ file:

<pre class="brush: xml; gutter: false">&lt;!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" &gt;
 
&lt;suite name="My first TestNG test suite" verbose="1" &gt;
  &lt;parameter name="username" value="john"/&gt;
  &lt;parameter name="password" value="demo"/&gt;
  &lt;test name="Login tests"   &gt;
    &lt;packages&gt;
      &lt;package name="com.ontestautomation.selenium.testng" /&gt;
   &lt;/packages&gt;
 &lt;/test&gt;
&lt;/suite&gt;</pre>

Another useful option of TestNG is the fact that it automatically generates readable HTML reports containing the test results. By default, these are created in a _test-output_ directory relative to your project location. The HTML report generated from the test above, for example, looks as follows:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_html.png" alt="TestNG HTML test report" width="1235" height="451" class="aligncenter size-full wp-image-685" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_html.png 1235w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_html-300x110.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_html-1024x374.png 1024w" sizes="(max-width: 1235px) 100vw, 1235px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/testng_test_results_html.png)  
You can further personalize and adjust the reports as described [here](http://testng.org/doc/documentation-main.html#test-results).

**Further reading**  
An Eclipse project including the TestNG test I&#8217;ve demonstrated above and the reports that have been generated can be downloaded [here](http://www.ontestautomation.com/files/SeleniumTestNG.zip).

Happy TestNG testing! Since I&#8217;ve got to know TestNG in the past couple of weeks, I&#8217;ve discovered quite a few interesting possibilities that I want to share with you in future posts, so stay tuned.