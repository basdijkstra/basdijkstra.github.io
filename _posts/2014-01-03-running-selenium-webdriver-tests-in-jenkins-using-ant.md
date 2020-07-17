---
id: 265
title: Running Selenium Webdriver tests in Jenkins using Ant
date: 2014-01-03T11:18:00+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=265
permalink: /running-selenium-webdriver-tests-in-jenkins-using-ant/
categories:
  - Selenium
tags:
  - ant
  - jenkins
  - selenium webdriver
---
In [a previous post](http://www.ontestautomation.com/data-driven-testing-in-selenium-webdriver-using-excel/) I introduced a very simple and straightforward way to run data-driven tests in Selenium Webdriver using test data stored in an Excel sheet. In this post, I want to show how to run these tests using a continuous integration (CI-) solution.

My preferred CI-tool is [Jenkins](http://jenkins-ci.org/), as it is open source, very flexible and easy to use.  
First, make sure that Jenkins is set up properly and is running as a service. Installation is very easy, so I won&#8217;t go into details here.

I also recommend using [Ant](http://ant.apache.org/) as a software build tool to further ease the process of compiling and running our tests. While it is not strictly necessary to use Ant, it will make life a lot easier for us. Again, install Ant and make sure it is running smoothly by typing _ant_ on the command prompt. If it starts asking for a build.xml file, it&#8217;s running properly.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/01/ant_running.png" alt="ant_running" width="310" height="162" class="aligncenter size-full wp-image-267" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/01/ant_running.png 310w, https://www.ontestautomation.com/wp-content/uploads/2014/01/ant_running-300x156.png 300w" sizes="(max-width: 310px) 100vw, 310px" />](http://www.ontestautomation.com/wp-content/uploads/2014/01/ant_running.png)

Next, open the Selenium Webdriver project in your IDE. Again, I prefer using Eclipse, so the images shown here will be based on Eclipse. Other IDEs such as IntelliJ usually provide the same functionality, it&#8217;s just hidden behind different menu options.

Before we start configuring our project for using Ant and running in Jenkins, we are going to add some flexibility to it. Jenkins uses its own workspace and might be running on another server altogether. However, our Selenium project contains a reference to a locally stored Excel data source. Therefore, we are going to add the possibility to provide the path to the data source to be used as an argument to our _main_ method in the Selenium test. In this way, we can simply specify the location of the data source when we run the test. Not only is this necessary to have our test run smoothly in Jenkins, it also adds the possibility to execute several test runs with separate test data sets.

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void main (String args[]) {
		
		String strPath;
		
		if (args.length == 1) {
			strPath = args[0];
		} else {
			strPath = "Z:\\Documents\\Bas\\blog\\datasources\\testdata.xls";
		}
		
		try {
			// Open the Excel file
			FileInputStream fis = new FileInputStream(new File(strPath).getAbsolutePath());</pre>

If an argument is specified when running the _main_ method of our test, we assume this is a relative path to our Excel data source. In order to be able to use it, all we have to do is to get the absolute path for it and off we go. If no argument is specified, we use the default Excel file.

Next, we create a _build.xml_ file that provides Ant with the necessary instructions and details on how to build and run our test. In Eclipse, this can be done easily by right-clicking our project and selecting &#8216;Export > General > Ant Buildfiles&#8217;. After selecting the appropriate project, a _build.xml_ file is generated and added to the root of our project. The example below is a part of the resulting file:

<pre class="brush: xml; gutter: false; first-line: 1; highlight: []; html-script: false">&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;
&lt;!-- WARNING: Eclipse auto-generated file.
              Any modifications will be overwritten.
              To include a user specific buildfile here, simply create one in the same
              directory with the processing instruction &lt;?eclipse.ant.import?&gt;
              as the first entry and export the buildfile again. --&gt;&lt;project basedir="." default="build" name="seleniumTest"&gt;
&lt;property environment="env"/&gt;
&lt;property name="ECLIPSE_HOME" value="C:/Tools/eclipse"/&gt;
&lt;property name="debuglevel" value="source,lines,vars"/&gt;
&lt;property name="target" value="1.7"/&gt;
&lt;property name="source" value="1.7"/&gt;
&lt;path id="seleniumTest.classpath"&gt;
    &lt;pathelement location="bin"/&gt;
    &lt;pathelement location="../../../../../vmware-host/Shared Folders/Documents/Bas/blog/libs/selenium-server-standalone-2.37.0.jar"/&gt;
    &lt;pathelement location="../../../../../vmware-host/Shared Folders/Documents/Bas/blog/libs/poi-3.9-20121203.jar"/&gt;
&lt;/path&gt;
&lt;target name="init"&gt;
    &lt;mkdir dir="bin"/&gt;
    &lt;copy includeemptydirs="false" todir="bin"&gt;
        &lt;fileset dir="src"&gt;
            &lt;exclude name="**/*.java"/&gt;
        &lt;/fileset&gt;
    &lt;/copy&gt;
&lt;/target&gt;
&lt;target name="clean"&gt;
    &lt;delete dir="bin"/&gt;
&lt;/target&gt;</pre>

Before we can run our tests using Ant, we need to make two modifications.

First, we need to clean up the classpath as we want to use clear and relative paths. Make sure that the necessary libraries can be found on the designated locations. These are specified relative to the location of the _build.xml_ file.

<pre class="brush: xml; gutter: false; first-line: 1; highlight: []; html-script: false">&lt;path id="seleniumTest.classpath"&gt;
    &lt;pathelement location="bin"/&gt;
    &lt;pathelement location="libs/selenium-server-standalone-2.37.0.jar"/&gt;
    &lt;pathelement location="libs/poi-3.9-20121203.jar"/&gt;
&lt;/path&gt;</pre>

Next, we need to make sure that our Excel data source is specified as an argument in the designated Ant target:

<pre class="brush: xml; gutter: false; first-line: 1; highlight: []; html-script: false">&lt;target name="ExcelDataDriven"&gt;
    &lt;java classname="com.ontestautomation.selenium.ExcelDataDriven" failonerror="true" fork="yes"&gt;
        &lt;arg line="datasources/testdata.xls"/&gt;
        &lt;classpath refid="seleniumTest.classpath"/&gt;
    &lt;/java&gt;
&lt;/target&gt;</pre>

Now, we can execute our test using Ant either in Eclipse or at the command line. When you choose the latter, go to the subdirectory for the Selenium project in your workspace (_build.xml_ should be located there) and execute _ant <<insertTargetNameHere>>_ (_ant ExcelDataDriven_ in this case). You&#8217;ll see that the test is run successfully using Ant.

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/01/ant_run_test.png" alt="ant_run_test" width="677" height="240" class="aligncenter size-full wp-image-271" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/01/ant_run_test.png 677w, https://www.ontestautomation.com/wp-content/uploads/2014/01/ant_run_test-300x106.png 300w" sizes="(max-width: 677px) 100vw, 677px" />](http://www.ontestautomation.com/wp-content/uploads/2014/01/ant_run_test.png)

The final step is to have this step performed by Jenkins. This should be very straightforward now. Create a new job in Jenkins and add &#8216;Invoke Ant&#8217; as a build step. Specify the correct target (again, _ExcelDataDriven_ in our case).

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_ant_target.png" alt="jenkins_ant_target" width="493" height="97" class="aligncenter size-full wp-image-274" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_ant_target.png 493w, https://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_ant_target-300x59.png 300w" sizes="(max-width: 493px) 100vw, 493px" />](http://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_ant_target.png)

Make sure that all referenced libraries and data sources can be found at the correct locations in the workspace for the Jenkins job (this is where using relative paths comes in handy!). Normally, you would do this using some sort of version control sytem such as Subversion. Next, schedule a build for the job, which should run smoothly now:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_test_output.png" alt="jenkins_test_output" width="718" height="228" class="aligncenter size-full wp-image-275" srcset="https://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_test_output.png 718w, https://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_test_output-300x95.png 300w" sizes="(max-width: 718px) 100vw, 718px" />](http://www.ontestautomation.com/wp-content/uploads/2014/01/jenkins_test_output.png)

That&#8217;s it, we&#8217;ve now successfully run our Selenium Webdriver tests in Jenkins using Ant. One step closer to a successful continuous integration approach!