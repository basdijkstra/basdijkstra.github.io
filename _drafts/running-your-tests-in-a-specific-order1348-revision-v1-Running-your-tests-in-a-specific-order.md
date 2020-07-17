---
id: 1357
title: Running your tests in a specific order
date: 2016-03-30T06:28:23+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1348-revision-v1/
permalink: /1348-revision-v1/
---
General consensus within the test automation community is that your automated tests should be able to run independently. That is, tests should be runnable in any given order and the result of a test should not depend on the outcome of one or more previous tests. This is generally a good practice and one I try and adhere to as much as possible. Nothing worse than a complete test run being wasted because some initial test data setup actions failed, causing all other tests to fail as well. Especially when you&#8217;re talking about end-to-end user interface tests that only run overnight because they&#8217;re taking so awfully long to finish.

However, in some cases, having your tests run in a specific order can be the most pragmatic or even the only (practical) option. For example, in my current project I am automating of a number of regression tests that require specific test data to be present before the actual checks can be performed. And since:

  * I don&#8217;t want to rely on test data that&#8217;s already being present in the database
  * Restoring a database snapshot is not a feasible option (at least not at the moment)
  * Creation of suitable test data takes at least a minute but closer to two for most tests. This has to be done through the user interface since a lot of data is stored in blobs, making SQL updates a challenging strategy to say the least..

the only viable option is to make the creation of test data the first step in a test run. Creating the necessary test data before each and every individual test would just take too long.

Since we&#8217;re using <a href="http://www.specflow.org/" target="_blank">SpecFlow</a>, we&#8217;re creating test data in the first scenario in every feature. All other scenarios in the feature rely on this test data. This means that the test data creation scenario needs to be run first, otherwise the subsequent scenarios will fail. Using <a href="https://github.com/cucumber/cucumber/wiki/Background" target="_blank">Background</a> is not an option, because those steps are run before each individual scenario, whereas we want to run the test data creation steps once every feature.

The above situation is just one example of a case where being able to control the execution order of your tests can come in very useful. Luckily, most testing frameworks support this in one or more ways. In the remainder of this post, we&#8217;re going to have a look at how you can define test execution order in <a href="http://junit.org/junit4/" target="_blank">JUnit</a>, <a href="http://testng.org/doc/index.html" target="_blank">TestNG</a> and <a href="http://nunit.org/" target="_blank">NUnit</a>.

**JUnit**  
Before version 4.11, JUnit did not support controlling the test execution order. However, newer versions of the test framework allow you to annotate your test classes using _@FixMethodOrder_, which enables you to select one of various _MethodSorters_. For example, the tests in this class are run in ascending alphabetical order, sorted by test method name:

<pre class="brush: java; gutter: false">@FixMethodOrder(MethodSorters.NAME_ASCENDING)
public class JUnitOrderedTests {
	
	@Test
	public void thirdMethod() {		
	}
	
	@Test
	public void secondMethod() {		
	}
	
	@Test
	public void firstMethod() {		
	}
}</pre>

Running these tests show that they are executed in the specified order:  
<a href="http://www.ontestautomation.com/running-your-tests-in-a-specific-order/junit_fixmethodorder_result/" rel="attachment wp-att-1349"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/junit_fixmethodorder_result.png" alt="JUnit FixMethodOrder result" width="226" height="92" class="aligncenter size-full wp-image-1349" /></a>

**TestNG**  
TestNG offers no less than three ways to order your tests:

_Using preserve-order in the testng.xml file_  
You can use the _preserve-order_ attribute in the _testng.xml_ file (where you specify which tests will be run) to have TestNG run the tests in the order they appear in the XML file:

<pre class="brush: xml; gutter: false">&lt;test name="OrderedTestNGTests" preserve-order="true"&gt;
	&lt;classes&gt;
		&lt;class name="TestNGTestClass"&gt;
			&lt;methods&gt;
				&lt;include name="testOne" /&gt;
				&lt;include name="testTwo" /&gt;
			&lt;/methods&gt;
		&lt;/class&gt;
	&lt;/classes&gt;
&lt;/test&gt;</pre>

_Using the priority attribute_  
You can also use the _priority_ attribute in your _@Test_ annotation to prioritize your test methods and determine the order in which they are run:

<pre class="brush: java; gutter: false">public class TestNGPrioritized {
	
	@Test(priority = 3)
	public void testThree() {		
	}
	
	@Test(priority = 1)
	public void testOne() {		
	}
	
	@Test(priority = 2)
	public void testTwo() {		
	}	
}</pre>

_Using dependencies_  
In TestNG, you can have tests and test suites depend on other tests / test suites. This also implicitly defines the order in which the tests are executed: when test A depends on test B, test B will automatically be run before test A. These dependencies can be defined in code:

<pre class="brush: java; gutter: false">public class TestNGOrderedTests {
	
	@Test(dependsOnMethods = {"parentTest"})
	public void childTest() {		
	}
	
	@Test
	public void parentTest() {		
	}
}</pre>

This works on method level (using _dependsOnMethods_) as well as on group level (using _dependsOnGroups_). Alternatively, you can define dependencies on group level in the _testng.xml_ file:

<pre class="brush: xml; gutter: false">&lt;test name="TestNGOrderedTests"&gt;
	&lt;groups&gt;
		&lt;dependencies&gt;
			&lt;group name="parenttests" /&gt;
			&lt;group name="childtests" depends-on="parenttests" /&gt;
		&lt;/dependencies&gt;
	&lt;/groups&gt;
&lt;/test&gt;</pre>

**NUnit**  
NUnit (the .NET version of the xUnit test frameworks) does not offer an explicit way to order tests, but you can control the order in which they are executed by naming your methods appropriately. Tests are run in alphabetical order, based on their method name. Note that this is an undocumented feature that may be altered or removed at will, but it still works in NUnit 3, which was recently released, and I happily abuse it in my current project..

At the beginning of this post, I mentioned that in my current project, we use SpecFlow to specify our regression tests. We then execute our SpecFlow scenarios using NUnit as a test runner, so we can leverage this alphabetical test order &#8216;trick&#8217; by naming our SpecFlow scenarios alphabetically inside a specific feature. This gives us a way to control the order in which our scenarios are executed:

<pre class="brush: text; gutter: false">Scenario: 01 Create test data
	Given ...
	When ...
	Then ...
	
Scenario: 02 Modify data
	Given ...
	When ...
	Then ...

Scenario: 03 Remove modified data
	Given ...
	When ...
	Then ...</pre>

Again, it is always best to create your tests in such a way that they can be run independently. However, sometimes this just isn&#8217;t possible or practical. In those cases, you can employ one of the strategies listed in this post to control your test order execution.