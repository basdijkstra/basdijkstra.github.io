---
id: 477
title: How to create screenshots in your Selenium Webdriver tests
date: 2014-06-10T07:15:47+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=477
permalink: /how-to-create-screenshots-in-your-selenium-webdriver-tests/
categories:
  - Selenium
tags:
  - create screenshot
  - java
  - selenium webdriver
---
As they say, a picture often says more than a thousand words. This also applies to test execution reports &#8211; no matter the clarity of your error messages, often a screenshot of the status of your browser instance at the moment a particular error occurs is the most efficient way to record what has gone wrong. In this post, I will show you how to generate a screenshot in Selenium Webdriver, which you can then save to disk for later reference and/or include in your custom reports.

First, we need a simple test script in which a screenshot is created at a given moment. Here it is:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runTest() {

	WebDriver driver = new FirefoxDriver();

	driver.get("http://parabank.parasoft.com");

	// log in
	driver.findElement(By.name("username")).sendKeys("john");
	driver.findElement(By.name("password")).sendKeys("demo");
	driver.findElement(By.xpath("//input[@value=&#039;Log In&#039;]")).click();
		
	//create screenshot
	createScreenshot(driver,"C:\\temp\\screen.png");

	driver.quit();
}</pre>

The _createScreenshot_ method takes two arguments: the driver instance and a physical location where the created screenshot will be stored. The implementation of this method is pretty straightforward:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void createScreenshot(WebDriver driver, String location) {

	// generate screenshot as a file object
	File scrFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
	try {
		// copy file object to designated location
		FileUtils.copyFile(scrFile, new File(location));
	} catch (IOException e) {
		System.out.println("Error while generating screenshot:\n" + e.toString());
	}
}</pre>

When we run this test, we see that a screenshot is created and stored in the location we provided (in my case, _C:\temp_):  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/06/screen.png" alt="Screenshot as created by our code" width="1049" height="1071" class="aligncenter size-full wp-image-478" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/06/screen.png 1049w, https://www.ontestautomation.com/wp-content/uploads/2014/06/screen-293x300.png 293w, https://www.ontestautomation.com/wp-content/uploads/2014/06/screen-1002x1024.png 1002w" sizes="(max-width: 1049px) 100vw, 1049px" />](http://www.ontestautomation.com/wp-content/uploads/2014/06/screen.png)

**Creating screenshots when an alert is active**  
One of my readers, Kritika Gupta, nicely alerted me that the method described above does not work when there is a Javascript alert active. Instead, an _UnhandledAlertException_ is thrown and no screenshot is created. This seems to be a known issue with Selenium, as can be read [here](https://code.google.com/p/selenium/issues/detail?id=4412). I have found a way to circumvent this problem by using the Java _Robot_ class. It works as follows:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void createScreenshotUsingRobot(WebDriver driver, String location) {
		
	BufferedImage image = null;
	try {
		image = new Robot().createScreenCapture(new Rectangle(Toolkit.getDefaultToolkit().getScreenSize()));
	} catch (HeadlessException | AWTException e1) {
		e1.printStackTrace();
	}
	try {
		ImageIO.write(image, "png", new File(location));
	} catch (IOException e) {
		e.printStackTrace();
	}
}</pre>

When we use this method to create screenshot, it even works when Javascript alert messages are active, as can be seen in the screenshot below, which has been taken using the code above:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/06/screen_using_robot.png" alt="Screenshot taken using the Java Robot class" width="1920" height="1200" class="aligncenter size-full wp-image-575" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/06/screen_using_robot.png 1920w, https://www.ontestautomation.com/wp-content/uploads/2014/06/screen_using_robot-300x187.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/06/screen_using_robot-1024x640.png 1024w" sizes="(max-width: 1920px) 100vw, 1920px" />](http://www.ontestautomation.com/wp-content/uploads/2014/06/screen_using_robot.png)  
Please note that the code above takes a screenshot of your complete desktop (as you can see) instead of a screenshot from the active browser window only. Also, remember that this code does not take care of handling the popup. This should be done in your main test code.

The Eclipse project I used for this example can be downloaded [here](http://www.ontestautomation.com/files/createScreenshot.zip).