---
id: 656
title: Running Selenium JUnit tests from Jenkins
date: 2014-12-16T09:29:05+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=656
permalink: /running-selenium-junit-tests-from-jenkins/
categories:
  - Continuous integration
  - Selenium
tags:
  - continuous integration
  - jenkins
  - junit
  - selenium webdriver
---
In this post I want to show you how to use Jenkins to automatically execute Selenium tests written in JUnit format, and how the results from these tests can be directly reported back to Jenkins. To achieve this, we need to complete the following steps:

  * Write some Selenium tests in JUnit format that we want to execute
  * Create a build file that runs these tests and writes the reports to disk
  * Set up a Jenkins job that runs these tests and interprets the results

_**Note:** First of all a point of attention: I couldn&#8217;t get this to work while Jenkins was installed as a Windows service. This has something to do with Jenkins opening browser windows and subsequently not having suitable permissions to access sites and handle Selenium calls. I solved this by starting Jenkins &#8216;by hand&#8217; by downloading the .war file from the [Jenkins site](http://jenkins-ci.org/) and running it using `java -jar jenkins.war`_

**Creating Selenium tests to run**  
First, we need to have some tests that we would like to run. I&#8217;ve created three short tests in JUnit-format, where one has an intentional error for demonstration purposes &#8211; it&#8217;s good practice to see if any test defects actually show up in Jenkins! Using the JUnit-format implies that tests can be run independently, so there can&#8217;t be any dependencies between tests. My test class looks like this (I&#8217;ve removed two tests and all import statements for brevity):

<pre class="brush: java; gutter: false">package com.ontestautomation.selenium.ci;

public class SeleniumCITest {
	
	static WebDriver driver;
	
	@Before
	public void setup() {
		
		driver = new FirefoxDriver();
		driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);				
	}
	
	@Test
	public void successfulLoginLogout() {
		
		driver.get("http://parabank.parasoft.com");
		Assert.assertEquals(driver.getTitle(), "ParaBank | Welcome | Online Banking");
		driver.findElement(By.name("username")).sendKeys("john");
		driver.findElement(By.name("password")).sendKeys("demo");
		driver.findElement(By.cssSelector("input[value=&#039;Log In&#039;]")).click();
		Assert.assertEquals(driver.getTitle(), "ParaBank | Accounts Overview");
		driver.findElement(By.linkText("Log Out")).click();
		Assert.assertEquals(driver.getTitle(), "ParaBank | Welcome | Online Banking");
	}
	
	@After
	public void teardown() {
		driver.quit();
	}	
}</pre>

Pretty straightforward, but good enough.

**Creating a build file to run tests automatically**  
Now to create a build file to run our tests automatically. I used Ant for this, but Maven should work as well. I had Eclipse generate an Ant build-file for me, then changed it to allow Jenkins to run the tests as well. In my case, I only needed to change the location of the imports (the Selenium and the JUnit .jar files) to a location where Jenkins could find them:

<pre class="brush: xml; gutter: false">&lt;path id="seleniumCI.classpath"&gt;
    &lt;pathelement location="bin"/&gt;
    &lt;pathelement location="C:/libs/selenium-server-standalone-2.44.0.jar"/&gt;
    &lt;pathelement location="C:/libs/junit-4.11.jar"/&gt;
&lt;/path&gt;</pre>

Note that I ran my tests on my own system, so in this case it&#8217;s OK to use absolute paths to the .jar files, but it&#8217;s by no means good practice to do so! It&#8217;s better to use paths relative to your Jenkins workspace, so tests and projects are transferable and can be run on any system without having to change the build.xml.

Actual test execution is a matter of using the _junit_ and _junitreport_ tasks:

<pre class="brush: xml; gutter: false">&lt;target name="SeleniumCITest"&gt;
    &lt;mkdir dir="${junit.output.dir}"/&gt;
    &lt;junit fork="yes" printsummary="withOutAndErr"&gt;
        &lt;formatter type="xml"/&gt;
        &lt;test name="com.ontestautomation.selenium.ci.SeleniumCITest" todir="${junit.output.dir}"/&gt;
        &lt;classpath refid="seleniumCI.classpath"/&gt;
        &lt;bootclasspath&gt;
            &lt;path refid="run.SeleniumCITest (1).bootclasspath"/&gt;
        &lt;/bootclasspath&gt;
    &lt;/junit&gt;
&lt;/target&gt;
&lt;target name="junitreport"&gt;
    &lt;junitreport todir="${junit.output.dir}"&gt;
        &lt;fileset dir="${junit.output.dir}"&gt;
            &lt;include name="TEST-*.xml"/&gt;
        &lt;/fileset&gt;
        &lt;report format="frames" todir="${junit.output.dir}"/&gt;
    &lt;/junitreport&gt;
&lt;/target&gt;</pre>

This is generated automatically when you create your build.xml using Eclipse, by the way.

**Running your tests through Jenkins**  
The final step is setting up a Jenkins job that simply calls the correct Ant target in a build step:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/ant_build_step.png" alt="Ant build step" width="1263" height="189" class="aligncenter size-full wp-image-666" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/ant_build_step.png 1263w, https://www.ontestautomation.com/wp-content/uploads/2014/12/ant_build_step-300x44.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/12/ant_build_step-1024x153.png 1024w" sizes="(max-width: 1263px) 100vw, 1263px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/ant_build_step.png)  
After tests have been run, Jenkins should pick up the JUnit test results from the folder specified in the _junitreport_ task in the Ant build.xml:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/post_build_action.png" alt="JUnit report post build action" width="1255" height="256" class="aligncenter size-full wp-image-667" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/post_build_action.png 1255w, https://www.ontestautomation.com/wp-content/uploads/2014/12/post_build_action-300x61.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/12/post_build_action-1024x208.png 1024w" sizes="(max-width: 1255px) 100vw, 1255px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/post_build_action.png)  
If everything is set up correctly, you should now be able to run your tests through Jenkins and have the results displayed:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_build_result.png" alt="Build result in Jenkins" width="509" height="248" class="aligncenter size-full wp-image-668" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_build_result.png 509w, https://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_build_result-300x146.png 300w" sizes="(max-width: 509px) 100vw, 509px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_build_result.png)  
You can also view details on the individual test results by clicking on the error message:  
[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_error_message_details.png" alt="Error details in Jenkins" width="1145" height="202" class="aligncenter size-full wp-image-669" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_error_message_details.png 1145w, https://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_error_message_details-300x52.png 300w, https://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_error_message_details-1024x180.png 1024w" sizes="(max-width: 1145px) 100vw, 1145px" />](http://www.ontestautomation.com/wp-content/uploads/2014/12/jenkins_error_message_details.png)

The Eclipse project I have used for this example can be downloaded [here](http://www.ontestautomation.com/files/seleniumCI.zip).