---
id: 1250
title: An introduction to mutation testing and PIT
date: 2016-02-09T12:28:27+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1240-revision-v1/
permalink: /1240-revision-v1/
---
This blog post will cover the basics of the concept of mutation testing. I have been made aware of mutation testing only recently, but I have discovered it&#8217;s a very powerful and interesting technique for:

  * analysis and improvement of unit tests
  * detection of dead code in your application

Two things that are always worth taking a look at if you ask me. I will illustrate the mutation testing concept using a tool called PIT and a simple piece of code and accompanying set of unit tests.

**What is mutation testing?**  
From <a href="https://en.wikipedia.org/wiki/Mutation_testing" target="_blank">Wikipedia</a>:

> Mutation testing is used to design new software tests and evaluate the quality of existing software tests. Mutation testing involves modifying a program in small ways. Each mutated version is called a mutant and tests detect and reject mutants by causing the behavior of the original version to differ from the mutant. This is called killing the mutant. Test suites are measured by the percentage of mutants that they kill.

In other words, mutation testing is a technique that allows you to evaluate not only the percentage of code that is executed when running your tests (i.e., code coverage), but also the ability of your tests to detect any defects in the executed code. This makes mutation testing a very powerful and very useful technique I think anyone involved in software development and testing should at least be aware of.

**Introducing PIT**  
I will try and illustrate the power of mutation testing using PIT, a Java mutation test tool which can be downloaded <a href="http://pitest.org/" target="_blank">here</a>. I chose PIT over other available mutation test tools mainly because of its ease of installation and use.

Assuming you&#8217;re also using Maven, you can configure your Java project for mutation testing using PIT by adding the following to your _pom.xml_:

<pre class="brush: xml; gutter: false">&lt;build&gt;
	&lt;plugins&gt;
		&lt;plugin&gt;
			&lt;groupId&gt;org.pitest&lt;/groupId&gt;
			&lt;artifactId&gt;pitest-maven&lt;/artifactId&gt;
			&lt;version&gt;PIT-VERSION&lt;/version&gt;
			&lt;configuration&gt;
				&lt;targetClasses&gt;
					&lt;param&gt;package.root.containing.classes.to.mutate*&lt;/param&gt;
				&lt;/targetClasses&gt;
				&lt;targetTests&gt;
					&lt;param&gt;package.root.containing.test.classes*&lt;/param&gt;
				&lt;/targetTests&gt;
			&lt;/configuration&gt;
		&lt;/plugin&gt;
	&lt;/plugins&gt;
&lt;/build&gt;</pre>

Simply replace the package locators with those appropriate for your project and be sure not to forget the asterisk at the end. Also replace PIT-VERSION with the PIT version you want to use (the latest is 1.1.4 at the moment of writing this blog post) and you&#8217;re good to go.

**The code class and test class to be subjected to mutation testing**  
I created a very simple Calculator class that, you guessed it, performs simple arithmetic on integers. My calculator only does addition, subtraction and power calculations:

<pre class="brush: java; gutter: false">public class Calculator {

	int valueDisplayed;

	public Calculator() {
		this.valueDisplayed = 0;
	}
	
	public Calculator(int initialValue) {
		this.valueDisplayed = initialValue;
	}

	public void add(int x) {
		this.valueDisplayed += x;
	}
	
	public void subtract(int x) {
		this.valueDisplayed -= x;
	}
	
	public void power(int x) {
		this.valueDisplayed = (int) Math.pow(this.valueDisplayed, x);
	}

	public int getResult() {
		return this.valueDisplayed;
	}
	
	public void set(int x) {
		this.valueDisplayed = x;
	}

	public boolean setConditional(int x, boolean yesOrNo) {
		if(yesOrNo) {
			set(x);
			return true;
		} else {
			return false;
		}
	}
}</pre>

To test the calculator, I have created a couple of TestNG unit tests that call the various methods my calculator supports. Note that PIT supports both JUnit and TestNG.

<pre class="brush: java; gutter: false">public class CalculatorTest {
	
	@Test
	public void testAddition() {
		
		Calculator calculator = new Calculator();
		calculator.add(2);
		Assert.assertEquals(calculator.getResult(), 2);
	}
	
	@Test
	public void testPower() {
		
		Calculator calculator = new Calculator(2);
		calculator.power(3);
		Assert.assertEquals(calculator.getResult(), 8);
	}
	
	@Test
	public void testConditionalSetTrue() {
		
		Calculator calculator = new Calculator();
		Assert.assertEquals(calculator.setConditional(2, true), true);
	}
	
	@Test
	public void testConditionalSetFalse() {
		
		Calculator calculator = new Calculator();
		Assert.assertEquals(calculator.setConditional(3, false), false);
	}
}</pre>

To illustrate the capabilities of PIT and mutation testing in general, I &#8216;forgot&#8217; to include a test for the subtract() method. Also, I created what is known as a &#8216;weak test&#8217;: a test that passes but doesn&#8217;t check whether all code is actually called (in this case, no check is done to see whether set() is called when calling _setConditional()_). Now, when we run PIT on our code and test classes using:

`mvn org.pitest:pitest-maven:mutationCoverage`

an HTML report is generated displaying our mutation test results:

<a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/pit_report/" rel="attachment wp-att-1246"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report.png" alt="The report as generated by PIT" width="845" height="317" class="aligncenter size-full wp-image-1246" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report.png 845w, https://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report-300x113.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report-768x288.png 768w" sizes="(max-width: 845px) 100vw, 845px" /></a>

When we drill down to our Calculator class we can see the modifications that have been made by PIT and the effect it had on our tests:

<a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/pit_report_details/" rel="attachment wp-att-1247"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report_details.png" alt="Class-level details of the PIT mutation test results" width="718" height="946" class="aligncenter size-full wp-image-1247" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report_details.png 718w, https://www.ontestautomation.com/wp-content/uploads/2016/02/pit_report_details-228x300.png 228w" sizes="(max-width: 718px) 100vw, 718px" /></a>

This clearly shows that our unit test suite has room for improvement:

  * The fact that _subtract()_ is never called in our test suite (i.e., our code coverage can be improved) is detected
  * The fact that the call to _set()_ can be removed from the code without our test results being affected (i.e., our tests are lacking defect detection power) is detected

These holes in our test coverage and test effectiveness might go unnoticed for a long time, especially since all tests pass when run using TestNG. This goes especially for the second flaw as a regular code coverage tool would not pick this up: the call to _set()_ is made after all, but it does not have any effect on the outcome of our tests!

**Additional PIT features**  
The <a href="http://pitest.org/quickstart/" target="_blank">PIT documentation</a> discusses a lot of features that make your mutation testing efforts even more powerful. You can configure the set of mutators used to tailor the result set to your needs, you can use mutation filters to filter out any unwanted results, and much more. However, even in the default configuration, using PIT (or potentially any other mutation testing tool as listed <a href="https://en.wikipedia.org/wiki/Mutation_testing" target="_blank">here</a> will tell you a lot about the quality of your unit testing efforts.

**Removing dead code from your codebase based on mutation test results**  
Apart from evaluating the quality of your unit tests, mutation test results can also give you insight into which parts of your application code are never executed (dead code). Consider the call to the _set()_ method in the example above. The mutation test results indicated that this call could be removed without the results of the unit test being altered. Now, in our case it is pretty obvious that this indicates a lack of coverage in our unit tests (if you want to set the Calculator value, you&#8217;d better call the _set()_ method), but it isn&#8217;t hard to imagine a situation where such a method call can be removed without any further consequences. In this case, the results of the mutation tests will point you to potentially dead code that might be a candidate for refactoring or removal. Thanks go to <a href="https://github.com/mbj" target="_blank">Markus Schirp</a> for pointing out this huge advantage of mutation testing to me on Twitter.

**Example project**  
The Maven project that was used to generate the results demonstrated in this post can be downloaded <a href="http://www.ontestautomation.com/files/MutationTestingDemo.zip" target="_blank">here</a>. You can simply import this project and run

`mvn org.pitest:pitest-maven:mutationCoverage`

to recreate my test results and review the generated report. This will serve as a good starting point for your further exploration of the power and possibilities of mutation testing.