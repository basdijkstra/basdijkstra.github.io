---
id: 756
title: Creating HTML reports for your Selenium tests using ExtentReports
date: 2015-02-05T06:20:08+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=756
permalink: /creating-html-reports-for-your-selenium-tests-using-extentreports/
categories:
  - General test automation
  - Selenium
tags:
  - create screenshot
  - extentreports
  - html report
  - selenium webdriver
---
In a comment on [a previous post](http://www.ontestautomation.com/create-your-own-html-report-from-selenium-tests/) I wrote on creating custom HTML reports for Selenium tests, Anshoo Arora of [Relevant Codes](http://relevantcodes.com/) pointed me to [ExtentReports](http://relevantcodes.com/extentreports-for-selenium/), his own solution for generating HTML reports. This week, I have been playing around with it for a while and I must say I&#8217;m pretty impressed by its ease of use and the way the reports it generates turn out. Let&#8217;s have a look.

_Note: this post is based on ExtentReports version 1.4 and therefore might not reflect any changes made in more recent versions._

**Downloading and installation**  
Installation of ExtentReports is a breeze. Just download the latest .jar from the [website](http://relevantcodes.com/extentreports-for-selenium/), add it as a dependency to your Java project and you&#8217;re all set.

**An example test and report**  
To illustrate the workings of ExtentReports, we&#8217;ll use two simple login tests that are executed against the [ParaBank](http://parabank.parasoft.com/parabank/index.htm) demo application on the Parasoft website. Both tests perform a login action, where the first will be successful and the second one will be unsuccessful due to invalid credentials. We&#8217;ll have ExtentReports generate a HTML report that contain the appropriate test results.  
<!--more-->

<pre class="brush: java; gutter: false">public class ExtentReportsDemo {

	static final ExtentReports extent = ExtentReports.get(ExtentReportsDemo.class);
	static WebDriver driver = new FirefoxDriver();

	static String reportLocation = "C:\\Tools\\reports\\";
	static String imageLocation = "images/";
	
	public static void main (String args[]) {

		extent.init(reportLocation + "ScreenshotReport.html", true, DisplayOrder.BY_OLDEST_TO_LATEST, GridType.STANDARD);

        extent.config().documentTitle("Sample ExtentReports report");
        
		extent.config().reportHeadline("Test report for ParaBank login tests generated using &lt;b&gt;ExtentReports&lt;/b&gt;");

		extent.startTest("TC01.1","This test is a positive login test for ParaBank");
		runPositiveTest();
		extent.endTest();

		extent.startTest("TC01.2","This is a negative login test for ParaBank (contains a defect)");
		runNegativeTest();
		extent.endTest();

		driver.quit();
	}

	public static void runPositiveTest() {

		driver.get("http://parabank.parasoft.com");

		checkTitle(driver,"ParaBank | Welcome | Online Banking");

		driver.findElement(By.name("username")).sendKeys("john");
		driver.findElement(By.name("password")).sendKeys("demo");
		driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();

		checkTitle(driver,"ParaBank | Accounts Overview");

		driver.findElement(By.partialLinkText("Log Out")).click();
	}

	public static void runNegativeTest() {

		driver.get("http://parabank.parasoft.com");

		checkTitle(driver,"ParaBank | Welcome | Online Banking");

		driver.findElement(By.name("username")).sendKeys("john");
		driver.findElement(By.name("password")).sendKeys("incorrectpassword");
		driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();

		checkErrorMessage(driver,"Incorrect credentials");
	}

	public static void checkTitle(WebDriver driver, String expectedTitle) {

		if(driver.getTitle().equals(expectedTitle)) {
			extent.log(LogStatus.PASS, "Check page title", "Page title is " + expectedTitle);
		} else {
			extent.log(LogStatus.FAIL, "Check page title", "Incorrect login page title " + driver.getTitle());
		}
	}

	public static void checkErrorMessage(WebDriver driver, String expectedMessage) {

		String errorMessage = driver.findElement(By.className("error")).getText();

		if(errorMessage.equals(expectedMessage)) {
			extent.log(LogStatus.PASS, "Check error message", "Error message is " + expectedMessage);
		} else {
			extent.log(LogStatus.FAIL, "Check error message","View details below:",createScreenshot(driver));
		}
	}
}</pre>

As you can see, using ExtentReports is very straightforward. All we need to do is initialize the report by specifying:

  * The location where the report needs to be stored
  * If any existing reports with the same name should be overwritten
  * Whether the tests in the report should appear in chronological (_DisplayOrder.BY\_OLDEST\_TO_LATEST_) or antichronological order
  * Whether the grid type used for the results should be standard (_GridType.STANDARD_) or using the [Masonry](http://masonry.desandro.com/) framework (_GridType.MASONRY_)

With _.config().documentTitle(&#8220;Your report title&#8221;)_ you can set the HTML document title for your report.

We can also specify a custom header using _.config().reportHeadline(&#8220;place header here&#8221;)_. This header can also contain HTML code, which gives you a wide array of options for creating a nice looking header for your report.

For every test, we start a test-specific report using _.startTest()_, and we end recording reporting events using _.endTest()_. _.startTest()_ takes two parameters, the first is the test name or identifier, the second is a description that is added to the report as we will see. _.endTest()_ ends the report activities for the test that has been started earlier. As of version 1.0, explicitly ending a test using _.endTest()_ is no longer required when you want to start a new test.

Logging actual events is straightforward as well using the _.log()_ method. Logging events can be created on six different levels (their use as presented here are suggestions only):

  * PASS for checks that are executed with a positive result
  * ERROR for logging any (non-fatal) error that occurs during test execution
  * WARNING for logging warnings
  * FAIL for checks that are executed with a negative result
  * INFO for logging additional info relevant to the test (or for debugging purposes)
  * FATAL for logging fatal errors occurring during test execution

Since we can include HTML markup everywhere in the report, we can also add screenshots wherever we want. To do this, all we need to do is to add a slightly modified version of the screenshot method I introduced [here](http://www.ontestautomation.com/how-to-create-screenshots-in-your-selenium-webdriver-tests/):

<pre class="brush: java; gutter: false">public static String createScreenshot(WebDriver driver) {

	UUID uuid = UUID.randomUUID();

	// generate screenshot as a file object
	File scrFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
	try {
		// copy file object to designated location
		FileUtils.copyFile(scrFile, new File(reportLocation + imageLocation + uuid + ".png"));
	} catch (IOException e) {
		System.out.println("Error while generating screenshot:\n" + e.toString());
	}
	return imageLocation + uuid + ".png";
}</pre>

Note that I&#8217;ve predefined the report folder (an absolute path) and image subfolder (a subdirectory of the report location) somewhere else in the code. This method is called in the example above when we log an error and want to have a screenshot added to the report.

The result can be viewed <a href="http://www.ontestautomation.com/files/reports/ScreenshotReport.html" target="_blank">here</a> (the link to the screenshot is in the results for the second test). The same report using the Masonry framework can be viewed <a href="http://www.ontestautomation.com/files/reports/ScreenshotReportMasonry.html" target="_blank">here</a>. You&#8217;re free to play around with the various buttons to filter, expand and collapse test results and change the overall layout of the report a bit to have a look at the various display options.

**More configuration options**  
Apart from the options already used in the examples above, ExtentReports offers a couple of other useful features. If you really want to give your HTML reports a customized look, you can apply your own specific CSS simply by specifying it during report initialization as follows:

<pre class="brush: java; gutter: false">extent.config().addCustomStylesheet("C:\\path\\to\\yourcustomcss.css");</pre>

Alternatively, you can use inline style definitions as follows:

<pre class="brush: java; gutter: false">String style = "p{font-size:20px;}";
extent.config().addCustomStyles(style);</pre>

You can also add your own JavaScript code to your report:

<pre class="brush: java; gutter: false">extent.config().insertJS("$(&#039;.test&#039;).click(function(){ alert(&#039;test clicked&#039;); });");</pre>

You can also disable the footer that is displayed at the bottom of the page:

<pre class="brush: java; gutter: false">extent.config().removeExtentFooter();</pre>

You can also include a screenshot in the report without having to log a test step:

<pre class="brush: java; gutter: false">extent.attachScreenshot(createScreenshot(driver),"This is a sample screenshot without an accompanying log step");</pre>

Finally, you can exchange the standard icons for any other icon included in the [FontAwesome](http://fortawesome.github.io/Font-Awesome/) library:

<pre class="brush: java; gutter: false">extent.config().statusIcon(LogStatus.PASS, "check-circle");</pre>

Since version 1.4, a lot of new features have been added. For the complete documentation please review the usage documentation at http://relevantcodes.com/extentreports-documentation/.

**Further notes**  
As you have seen in this post, ExtentReports is a very useful library for quick generation of great looking HTML reports. Although ExtentReports is labeled as a solution for generating HTML reports for Selenium tests, it&#8217;s a generic solution that can be applied easily to other test frameworks, as long as they&#8217;re built in Java.

Thanks again to Anshoo Arora for pointing me to [ExtentReports](http://relevantcodes.com/extentreports-for-selenium/).