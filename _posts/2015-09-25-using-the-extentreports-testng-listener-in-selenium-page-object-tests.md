---
id: 1069
title: Using the ExtentReports TestNG listener in Selenium Page Object tests
date: 2015-09-25T00:00:41+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1069
permalink: /using-the-extentreports-testng-listener-in-selenium-page-object-tests/
categories:
  - General test automation
  - Selenium
tags:
  - extentreports
  - html report
  - java
  - page object design
  - selenium webdriver
  - testng
---
In this (long overdue) post I would like to demonstrate how to use ExtentReports as a listener for TestNG. By doing so, you can use your regular TestNG assertions and still create nicely readable ExtentReports-based HTML reports. I&#8217;ll show you how to do this using a Selenium WebDriver test that uses the Page Object pattern.

**Creating the Page Objects**  
First, we need to create some page objects that we are going to exercise during our tests. As usual, I&#8217;ll use the Parasoft Parabank demo application for this. My tests cover the login functionality of the application, so I have created Page Objects for the login page, the error page where you land when you perform an incorrect login and the homepage (the AccountsOverviewPage) where you end up when the login action is successful. For those of you that have read my past <a href="http://www.ontestautomation.com/using-the-page-object-model-pattern-in-selenium-testng-tests/" target="_blank">post on Selenium and TestNG</a>, these Page Objects have been used in that example as well. The Page Object source files are included in the Eclipse project that you can download at the end of this post.

**Creating the tests**  
To demonstrate the reporting functionality, I have created three tests:

  * A test that performs a successful login &#8211; this one passes
  * A test that performs an unsuccessful login, where the check on the error message returns passes &#8211; this one also passes
  * A test that performs an unsuccessful login, where the check on the error message returns fails &#8211; this one fails

The tests look like this:

<pre class="brush: java; gutter: false">public class LoginTest {
	
 WebDriver driver;
     
    @BeforeSuite
    public void setUp() {
	         
        driver = new FirefoxDriver();
        driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
    }
	     
    @Parameters({"incorrectusername","incorrectpassword"})
    @Test(description="Performs an unsuccessful login and checks the resulting error message (passes)")
    public void testFailingLogin(String username, String incorrectpassword) {
	         
        LoginPage lp = new LoginPage(driver);
        ErrorPage ep = lp.incorrectLogin(username, incorrectpassword);
        Assert.assertEquals(ep.getErrorText(), "The username and password could not be verified.");
    }
	    
    @Parameters({"incorrectusername","incorrectpassword"})
    @Test(description="Performs an unsuccessful login and checks the resulting error message (fails)")
    public void failingTest(String username, String incorrectpassword) {
	         
        LoginPage lp = new LoginPage(driver);
        ErrorPage ep = lp.incorrectLogin(username, incorrectpassword);
        Assert.assertEquals(ep.getErrorText(), "This is not the error message you&#039;re looking for.");
    }
	    
    @Parameters({"correctusername","correctpassword"})
    @Test(description="Performs a successful login and checks whether the Accounts Overview page is opened")
    public void testSuccessfulLogin(String username, String incorrectpassword) {
	         
        LoginPage lp = new LoginPage(driver);
        AccountsOverviewPage aop = lp.correctLogin(username, incorrectpassword);
        Assert.assertEquals(aop.isAt(), true);
    }
	     
    @AfterSuite
    public void tearDown() {
         
        driver.quit();
    }
}</pre>

Pretty straightforward, right? I think this is a clear example of why using Page Objects and having the right Page Object methods make writing and maintaining tests a breeze.

**Creating the ExtentReports TestNG listener**  
Next, we need to define the TestNG listener that creates the ExtentReports reports during test execution:

<pre class="brush: java; gutter: false">public class ExtentReporterNG implements IReporter {
    private ExtentReports extent;
 
    @Override
    public void generateReport(List&lt;XmlSuite&gt; xmlSuites, List&lt;ISuite&gt; suites, String outputDirectory) {
        extent = new ExtentReports(outputDirectory + File.separator + "ExtentReportTestNG.html", true);
 
        for (ISuite suite : suites) {
            Map&lt;String, ISuiteResult&gt; result = suite.getResults();
 
            for (ISuiteResult r : result.values()) {
                ITestContext context = r.getTestContext();
 
                buildTestNodes(context.getPassedTests(), LogStatus.PASS);
                buildTestNodes(context.getFailedTests(), LogStatus.FAIL);
                buildTestNodes(context.getSkippedTests(), LogStatus.SKIP);
            }
        }
 
        extent.flush();
        extent.close();
    }
 
    private void buildTestNodes(IResultMap tests, LogStatus status) {
        ExtentTest test;
 
        if (tests.size() &gt; 0) {
            for (ITestResult result : tests.getAllResults()) {
                test = extent.startTest(result.getMethod().getMethodName());
 
                test.getTest().startedTime = getTime(result.getStartMillis());
                test.getTest().endedTime = getTime(result.getEndMillis());
 
                for (String group : result.getMethod().getGroups())
                    test.assignCategory(group);
 
                String message = "Test " + status.toString().toLowerCase() + "ed";
 
                if (result.getThrowable() != null)
                    message = result.getThrowable().getMessage();
 
                test.log(status, message);
 
                extent.endTest(test);
            }
        }
    }
 
    private Date getTime(long millis) {
        Calendar calendar = Calendar.getInstance();
        calendar.setTimeInMillis(millis);
        return calendar.getTime();        
    }
}</pre>

This listener will create an ExtentReports report called _ExtentReportTestNG.html_ in the default TestNG output folder _test-output_. This report lists all passed tests, then all failed tests and finally all tests that were skipped during execution. There&#8217;s no need to add specific ExtentReports log statements to your tests.

**Running the test and reviewing the results**  
To run the tests, we need to define a _testng.xml_ file that enables the listener we just created and runs all tests we want to run:

<pre class="brush: xml; gutter: false">&lt;?xml version="1.0" encoding="UTF-8"?&gt;&lt;!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" &gt;

&lt;suite name="Parabank login test suite" verbose="1"&gt;
	&lt;listeners&gt;
		&lt;listener class-name="com.ontestautomation.extentreports.listener.ExtentReporterNG" /&gt;
	&lt;/listeners&gt;
	&lt;parameter name="correctusername" value="john" /&gt;
	&lt;parameter name="correctpassword" value="demo" /&gt;
	&lt;parameter name="incorrectusername" value="wrong" /&gt;
	&lt;parameter name="incorrectpassword" value="credentials" /&gt;
	&lt;test name="Login tests"&gt;
		&lt;packages&gt;
			&lt;package name="com.ontestautomation.extentreports.tests" /&gt;
		&lt;/packages&gt;
	&lt;/test&gt;
&lt;/suite&gt;</pre>

When we run our test suite using this _testng.xml_ file, all tests in the _com.ontestautomation.extentreports.tests_ package are run and an ExtentReports HTML report is created in the default _test-output_ folder. The resulting report can be seen <a href="http://www.ontestautomation.com/files/reports/ExtentReportsTestNG.html" target="_blank">here</a>.

**More examples**  
More examples on how to use the ExtentReports listener for TestNG can be found on <a href="http://relevantcodes.com/testng-listener-using-extentreports/" target="_blank">the ExtentReports website</a>.

The Eclipse project I have created to demonstrate the above can be downloaded <a href="http://www.ontestautomation.com/files/ExtentReportsTestNG.zip" target="_blank">here</a>.