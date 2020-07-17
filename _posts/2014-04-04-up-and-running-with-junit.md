---
id: 312
title: 'Up and running with: JUnit'
date: 2014-04-04T10:21:12+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=312
permalink: /up-and-running-with-junit/
categories:
  - Test automation tools
tags:
  - junit
  - up and running
---
_This is the first article in a new series on tools used in test automation. Each of the articles in it will introduce a specific test tool and will show you how to get up and running with it. The focus will be on free and / or open source test tools as this allows everyone with an interest in the tool presented to get started using it right away._

**What is JUnit?**  
JUnit is a unit testing framework for Java. Is it part of a family of unit testing frameworks for a variety of programming languages, known collectively as xUnit. With JUnit, you can quickly develop and run unit tests for Java classes to verify their correctness.

**Where can I get JUnit?**  
JUnit can be downloaded from [here](https://github.com/junit-team/junit/wiki/Download-and-Install). However, when you use an IDE such as Eclipse or IntelliJ, JUnit comes with the installation, making getting started with JUnit test development even easier.

**How do I install and configure JUnit?**  
As I prefer using Eclipse, the instructions below show you how to start developing and running JUnit tests in Eclipse. Those using IntelliJ are referred to the [IntelliJ homepage](http://www.jetbrains.com/idea/).

To start using JUnit to run tests on your code, all you need to do is to add the JUnit library to your existing Java project. To do this, right-click on your project, select Properties, go to the Libraries tab and press the &#8216;Add Library&#8217; button. Select JUnit from the list and click &#8216;Next&#8217;. Then, select &#8216;JUnit 4&#8217; as the library version and click &#8216;Finish&#8217;. JUnit has now been added to your project libraries and you&#8217;re ready to go.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_add_library.png" alt="junit_add_library" width="732" height="555" class="aligncenter size-full wp-image-313" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_add_library.png 732w, https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_add_library-300x227.png 300w" sizes="(max-width: 732px) 100vw, 732px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_add_library.png)

**Creating a first test script**  
Now, let&#8217;s create a first JUnit test script. First of all, as JUnit tests are Java classes in themselves, we create a new source folder in our project to prevent test code getting mixed up with application code. Right-click on your project, select Properties and in the Source tab, select &#8216;Add Folder&#8217;. Name this folder &#8216;test&#8217; for instance and add it to your project.

It&#8217;s a good idea to structure the test code in your test folder similar to the application code to be tested. So, if the Java class you are writing tests for is in package _x.y.z_ in the &#8216;src&#8217; folder, your test code for this class is placed in package _x.y.z_ in the &#8216;test&#8217; folder. Create this package in the &#8216;test&#8217; folder by right-clicking it and selecting &#8216;New > Package&#8217;.

One of the features of Eclipse is that it can automatically generate JUnit test skeletons for you based on the definition of the class you&#8217;re writing tests for. To do so, right-click on the package you&#8217;ll place the test code in and select &#8216;New > JUnit Test Case&#8217;. Name your test class &#8211; I prefer adding &#8216;Test&#8217; to the name of the class to be tested, so tests for class Apple.java are placed in AppleTest.java &#8211; and select the class under test using the button &#8216;Select&#8217; next to &#8216;Class under test:&#8217;. Click &#8216;Next&#8217; and select the methods for which you want to generate JUnit tests.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_select_methods.png" alt="junit_select_methods" width="526" height="604" class="aligncenter size-full wp-image-315" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_select_methods.png 526w, https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_select_methods-261x300.png 261w" sizes="(max-width: 526px) 100vw, 526px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_select_methods.png)

Click &#8216;Finish&#8217; to generate JUnit test skeletons for the selected methods. The code generated should be similar to this:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">package com.ontestautomation.selenium.objectmap;

import static org.junit.Assert.*;

import org.junit.Test;

public class ObjectMapTest {

	@Test
	public void testObjectMap() {
		fail("Not yet implemented");
	}

	@Test
	public void testGetLocator() {
		fail("Not yet implemented");
	}

}</pre>

The annnotation _@Test_ is used by JUnit to indicate the start of a test case. The generated test code is neatly placed in the right package in your Java project:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_package_explorer.png" alt="junit_package_explorer" width="352" height="292" class="aligncenter size-full wp-image-316" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_package_explorer.png 352w, https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_package_explorer-300x248.png 300w" sizes="(max-width: 352px) 100vw, 352px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_package_explorer.png)

**Running your test**  
To run your JUnit tests, simply right-click the test code file and select &#8216;Run As > JUnit Test&#8217;. Your test methods will be executed and a new tab opens displaying the test results:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_1.png" alt="junit_test_results_1" width="352" height="283" class="aligncenter size-full wp-image-318" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_1.png 352w, https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_1-300x241.png 300w" sizes="(max-width: 352px) 100vw, 352px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_1.png)

Both tests fail for now as they have not yet been implemented. That is, no actual tests are being executed so far as we haven&#8217;t written the tests itself yet. Below you see a simple test for the _getLocator_ method that tests whether the object retrieved from the object map using this method is equal to the object that is expected.

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">@Test
public void testGetLocator() {
	ObjectMap objMap = new ObjectMap("objectmap.properties");
	try {
		By testLocator = objMap.getLocator("bing.homepage.textbox");
		assertEquals("Check testLocator object",By.id("sb_form_q"),testLocator);
	} catch (Exception e) {
		System.out.println("Error during JUnit test execution:\n" + e.toString());
	}
		
}</pre>

If we rerun the test code, we now see that the test for the _getLocator_ method passes, meaning we have successfully implemented and run our first JUnit test!

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_2.png" alt="junit_test_results_2" width="352" height="254" class="aligncenter size-full wp-image-322" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_2.png 352w, https://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_2-300x216.png 300w" sizes="(max-width: 352px) 100vw, 352px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/junit_test_results_2.png)

**Useful features**  
To write more advanced and better maintainable tests, JUnit provides some nice features. The most important of these features are:

  * The ability to test for expected exceptions. If you want to validate that the method you developed throws the right exception under the right circumstances, you can easily verify this with JUnit, using the _expected=NameOfException.class_ notation:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">@Test(expected=Exception.class)
public void testGetLocatorException() throws Exception {
	ObjectMap objMap = new ObjectMap("objectmap.properties");
	By testLocator = objMap.getLocator("unknownobject");
}</pre>

  * The ability to create test suites using the _@Suite_ annotation. Using this you can run a set of test classes by calling a single test suite class.
  * The ability to parameterize tests with test data, using the _@Parameterized_ annotation. Using this you can make your unit tests data driven and run the same tests using multiple sets of test data without the need for duplicate test code.
  * The ability to easily export test results to continuous integration frameworks, such as Jenkins. JUnit generates reports in an XML format that can easily be interpreted by Jenkins and similar CI frameworks. This results in a very readable graphical representation of your JUnit test results:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/04/build_result_test_detail.png" alt="build_result_test_detail" width="1139" height="297" class="aligncenter size-full wp-image-328" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/04/build_result_test_detail.png 1139w, https://www.ontestautomation.com/wp-content/uploads/2014/04/build_result_test_detail-300x78.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/04/build_result_test_detail-1024x267.png 1024w" sizes="(max-width: 1139px) 100vw, 1139px" />](http://www.ontestautomation.com/wp-content/uploads/2014/04/build_result_test_detail.png)

**Further reading**  
For more information on JUnit, you can visit the sites below:

  * [JUnit home page](http://junit.org/)
  * [A very useful JUnit tutorial by Lars Vogel](http://www.vogella.com/tutorials/JUnit/article.html)

An Eclipse project including the tests I’ve demonstrated above and the reports that have been generated can be downloaded [here](http://www.ontestautomation.com/files/ObjectMapJUnit.zip).

Happy unit testing!