---
id: 1483
title: Create your own HTML report from Selenium tests
date: 2016-06-08T12:23:05+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/377-revision-v1/
permalink: /377-revision-v1/
---
As I am learning more and more about using Selenium Webdriver efficiently (experiences I try to share through this blog), I&#8217;m slowly turning away from my original standpoint that user interface-based test automation is not for me. I am really starting to appreciate the power of Selenium, especially when you use proper test automation framework design patterns such as the Page Object pattern [I wrote about earlier](http://www.ontestautomation.com/using-the-page-object-design-pattern-in-selenium-webdriver/ "Using the Page Object Design pattern in Selenium Webdriver"). However, Selenium by default lacks one vital aspect of what makes a good test automation tool to me: proper reporting options. Luckily, as Selenium is so open, there&#8217;s lots of ways to build custom reporting yourself. This post shows one possible approach.

**My approach**  
Personally, I prefer HTML reports as they are highly customizable, relatively easy to build and can be easily distributed to other project team members. To build a nice HTML report, I use the following two step approach:

  * Execute tests and gather statistics about validations executed
  * Create HTML report from these statistics after test execution has finished

In this post I&#8217;ll use the following test script as an example. I created a page with five HTML text fields, for which I am going to validate the default text. Nothing really realistic, but remember it&#8217;s only used to illustrate my reporting concept.

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main (String args[]) {
		
	WebDriver driver = new HtmlUnitDriver();
	driver.get("http://www.ontestautomation.com/files/report_test.html");
		
	for (int i = 1; i &lt;=5; i++) {
		WebElement el = driver.findElement(By.id("textfield" + Integer.toString(i)));
		Assert.assertEquals(el.getAttribute("value"), "Text field " + Integer.toString(i));
	}
		
	driver.close();	
}</pre>

When we run this script, one error is generated as text field 4 contains a different default value (go to the URL in the script to see for yourself).

**Custom reporting functions**  
To be able to create a nice HTML report, we first need some custom reporting functions that store test results in a way we can reuse them later to generate our report. To achieve this, I created a _report_ method in a _Reporter_ class that stores validation results in a simple List:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void report(String actualValue,String expectedValue) {
	if(actualValue.equals(expectedValue)) {
		Result r = new Result("Pass","Actual value &#039;" + actualValue + "&#039; matches expected value");
		details.add(r);
	} else {
		Result r = new Result("Fail","Actual value &#039;" + actualValue + "&#039; does not match expected value &#039;" + expectedValue + "&#039;");
		details.add(r);
	}
}</pre>

The _Result_ object is a simple class with three class variables: _result_ (which is either Pass or Fail), a resultText (which is a custom description) and a URL for a screenshot (the use of which we will see later).

For every test we execute in our Selenium script, instead of using the standard TestNG / JUnit assertions, we use our own reporting function. You might want to throw an error as well when a validation fails, but I&#8217;m happy just to write it to my report and let test execution continue.

After test execution is finished, we are going to write the test results we gathered to a file. For this, I used an extremely simple HTML template (yes, I was too lazy even to indent it properly):

<pre class="brush: html; gutter: false; first-line: 1; highlight: []; html-script: false">&lt;html&gt;
&lt;head&gt;
&lt;title&gt;Test Report&lt;/title&gt;
&lt;head&gt;
&lt;body&gt;
&lt;h3&gt;Test results&lt;/h3&gt;
&lt;table&gt;
&lt;tr&gt;
&lt;th width="10%"&gt;Step&lt;/th&gt;
&lt;th width="10%"&gt;Result&lt;/th&gt;
&lt;th width="80%"&gt;Remarks&lt;/th&gt;
&lt;/tr&gt;
&lt;!-- INSERT_RESULTS --&gt;
&lt;/table&gt;
&lt;/body&gt;</pre>

In this template I am going to insert my test results, using a simple replace function

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void writeResults() {
	try {
		String reportIn = new String(Files.readAllBytes(Paths.get(templatePath)));
		for (int i = 0; i &lt; details.size();i++) {
			reportIn = reportIn.replaceFirst(resultPlaceholder,"&lt;tr&gt;&lt;td&gt;" + Integer.toString(i+1) + "&lt;/td&gt;&lt;td&gt;" + details.get(i).getResult() + "&lt;/td&gt;&lt;td&gt;" + details.get(i).getResultText() + "&lt;/td&gt;&lt;/tr&gt;" + resultPlaceholder);
		}
			
		String currentDate = new SimpleDateFormat("dd-MM-yyyy").format(new Date());
		String reportPath = "Z:\\Documents\\Bas\\blog\\files\\report_" + currentDate + ".html";
		Files.write(Paths.get(reportPath),reportIn.getBytes(),StandardOpenOption.CREATE);
			
	} catch (Exception e) {
		System.out.println("Error when writing report file:\n" + e.toString());
	}
}</pre>

Finally, we need to use these custom reporting functions in our test script:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main (String args[]) {
		
	WebDriver driver = new HtmlUnitDriver();
	Reporter.initialize();
	driver.get("http://www.ontestautomation.com/files/report_test.html");
		
	for (int i = 1; i &lt;=5; i++) {
		WebElement el = driver.findElement(By.id("textfield" + Integer.toString(i)));
		Reporter.report(el.getAttribute("value"), "Text field " + Integer.toString(i));
	}
		
	Reporter.writeResults();
	driver.close();	
}</pre>

The _initialize()_ method simply clears all previous test results by emptying the List we use to store our test results. When we run our test, the following HTML report is generated:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/05/html_test_report.png" alt="The HTML test report" width="511" height="236" class="aligncenter size-full wp-image-397" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/05/html_test_report.png 511w, https://www.ontestautomation.com/wp-content/uploads/2014/05/html_test_report-300x138.png 300w" sizes="(max-width: 511px) 100vw, 511px" />](http://www.ontestautomation.com/wp-content/uploads/2014/05/html_test_report.png)

Here, we can clearly see that our test results are now available in a nicely readable (though not yet very pretty) format. In one of my next posts, I am going to enhance these HTML reporting functions with some additional features, such as:

  * Screenshots in case of errors
  * Use of stylesheets for eye candy
  * Test execution statistics
  * &#8230;

Hopefully the above will get you started creating nicely readable HTML reports for your Selenium tests!

The Eclipse project used in the above example can be downloaded [here](http://www.ontestautomation.com/files/seleniumReporting.zip "Services"). The HTML report template can be downloaded [here](http://www.ontestautomation.com/files/report_template.html) (right click, save as).