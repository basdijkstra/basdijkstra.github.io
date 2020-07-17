---
id: 420
title: 'Up and running with: Selendroid'
date: 2014-05-13T12:34:43+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=420
permalink: /up-and-running-with-selendroid/
categories:
  - Test automation tools
tags:
  - android
  - mobile testing
  - selendroid
  - selenium webdriver
  - up and running
---
_This is the second article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is Selendroid?**  
Selendroid is a test automation framework for testing Android native and hybrid applications. Selendroid tests are written using the Selenium Webdriver client API, which allows for full integration with existing Selenium frameworks.

**Where can I get Selendroid?**  
Selendroid can be downloaded from the [Selendroid website](http://selendroid.io/).

**How do I install and configure Selendroid?**  
Before you can start setting up Selendroid and writing tests, you need to download and install the latest Android SDK first. Clear instructions on how to do this can be found [here](http://spring.io/guides/gs/android/). Make sure you also create at least one Android virtual device (AVD) and test whether it can be run properly, as we are going to need this virtual device to deploy our app under test and run our Selendroid tests on it.

NOTE: The instructions specify the creation of an AVD with the latest Android version (KitKat, API level 19). This is all wonderful, but if you&#8217;re (like me) working on a virtual machine or any other machine with limited resources, you&#8217;re probably better off creating an AVD based on Android Gingerbread (API level 10). You will need to install the correct SDK components for that API level using the Android SDK Manager. Oh, and it&#8217;s probably wise as well to use GPU emulation by enabling &#8216;Use host GPU&#8217; in the AVD settings while you&#8217;re at it. This will make your test run much faster. Or, in my case, it will make your test run at all.

After you have successfully installed the Android SDK, you can install Selendroid following [these instructions](http://selendroid.io/setup.html). When you&#8217;ve done so, start Selendroid and load the test app (the .apk file) they provide [here](http://selendroid.io/setup.html#getAut). When you point your browser to

`http://localhost:4444/wd/hub/status`

you&#8217;ll see something similar to this:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/05/wd_hub_status.png" alt="Selendroid is running" width="1403" height="118" class="aligncenter size-full wp-image-425" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/05/wd_hub_status.png 1403w, https://www.ontestautomation.com/wp-content/uploads/2014/05/wd_hub_status-300x25.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/05/wd_hub_status-1024x86.png 1024w" sizes="(max-width: 1403px) 100vw, 1403px" />](http://www.ontestautomation.com/wp-content/uploads/2014/05/wd_hub_status.png)  
As you can see, Selendroid is running and it has detected our newly created AVD.

**Creating a first test script**  
Now we&#8217;re done setting up our environment we can go to the interesting part. To start mobile testing using Selendroid, create a new Java project, add the Selendroid and Selenium .jar files as dependencies and create the following script:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runSelendroidTest() throws Exception {
		
	// specify test capabilities (your &#039;test environment&#039;)
	SelendroidCapabilities capa = new SelendroidCapabilities("io.selendroid.testapp:0.9.0");
		
	// explicitly state that we want to run our test on an Android API level 10 device
	capa.setPlatformVersion(DeviceTargetPlatform.ANDROID10);
		
	// explicitly state that we use an emulator (an AVD) for test execution rather than a physical device
	capa.setEmulator(true);

	// start a new WebDriver
	WebDriver driver = new SelendroidDriver(capa);
		
	// execute a very simple test
	WebElement inputField = driver.findElement(By.id("my_text_field"));
	Assert.assertEquals("true", inputField.getAttribute("enabled"));
	inputField.sendKeys("Selendroid");
	Assert.assertEquals("Selendroid", inputField.getText());
		
	// quit testing
	driver.quit();
		
}</pre>

The comments in the code really explain it all. You can instantly see that Selendroid tests are indeed very similar to regular Selenium tests, with the exception that they are run on an app that is deployed on an AVD rather than on a website that you access using a browser.

**Running your test**  
Running your Selendroid tests is done just like you&#8217;d run Selenium tests, so there&#8217;s really no need to go into more detail on that here.

When you run your tests, you&#8217;ll see that an Android emulator (the AVD) is started, the app is loaded, tests are executed and the emulator is closed again. Selendroid can also be used to execute tests on apps that are deployed on physical Android devices connected to your machine, something I haven&#8217;t tried myself.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/05/screenshot_test_app.png" alt="A screenshot of the test app" width="336" height="519" class="aligncenter size-full wp-image-430" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/05/screenshot_test_app.png 336w, https://www.ontestautomation.com/wp-content/uploads/2014/05/screenshot_test_app-194x300.png 194w" sizes="(max-width: 336px) 100vw, 336px" />](http://www.ontestautomation.com/wp-content/uploads/2014/05/screenshot_test_app.png)

**Useful features**  
Selendroid offers a lot of to the tester that wants to create more useful and more complex tests. For example, you can very accurately emulate a user swipe action from right to left using the following code snippet:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">WebElement pages = driver.findElement(By.id("some_id"));
TouchActions flick = new TouchActions(driver).flick(pages, -100, 0, 0);
flick.perform();</pre>

Using the instructions presented here, just try and see for yourself what you can do with Selendroid. And of course, please share your experiences here.

**Further reading**  
Again, the official Selendroid site can be found [here](http://selendroid.io/). It offers a lot of information on the possibilities and features of Selendroid.

Happy mobile testing!