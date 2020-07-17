---
id: 1677
title: An introduction to property-based testing with JUnit-Quickcheck
date: 2016-12-01T18:53:56+01:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1284-revision-v1/
permalink: /1284-revision-v1/
---
Even though I consider having an extensive set of unit and unit integration tests a good thing in almost any case, there is still a fundamental shortcoming with automated checks that I feel should be addressed more often. Almost all checks I come across are very much example-based, meaning that only a single combination of input values is being checked. This goes for unit-, API-level as well as UI-driven tests, by the way. Of course, this is already far better than not checking anything at all, but how do you make sure that your check passes (and thus your application works for all, or at least for a considerable subset of all possible input parameter combinations?

In most cases, it is computationally impossible to perform a check for every combination of all possible input values (have you ever tried to work out what all possible values for a single string input parameter are?), so we need an approach that is able to generate a lot of (preferably random) input values that satisfy a set of predicates, and subsequently verify whether the check holds for all these input values and value combinations. Enter property-based testing (see <a href="http://blog.jessitron.com/2013/04/property-based-testing-what-is-it.html" target="_blank">this post on Jessica Kerr&#8217;s blog</a> for an introduction to the concept).

**What is property based testing?**  
In short, property-based testing pretty much exactly addresses the problem described above: based on an existing check, randomly generate a lot of input parameter combinations that satisfy predefined properties and see if the check passes for each of these combinations. This includes using negative and empty parameter values and other edge cases, all to try and break the system (or prove its robustness, if you want to look at it from the positive side&#8230;). You can do this either manually &#8211; although this does constitute a lot of effort &#8211; or, and given the nature of this blog this is of course the preferred method, use a tool to do the menial work for you.

**Why should you use property based testing?**  
Again, the answer is in the first paragraph: because property-based testing will give you far more information about the functional correctness and the robustness of your software than executing mere example-based checks. Especially when combined with <a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/" target="_blank">mutation testing</a>, property-based testing you will likely have you end up with a far more powerful and effective unit- and unit integration test suite.

**Tool: JUnit-Quickcheck**  
So, what tools are available for property-based testing? The archetypical tool is <a href="https://hackage.haskell.org/package/QuickCheck" target="_blank">QuickCheck</a> for the Haskell language. Most other property-based testing tools are somehow derived from QuickCheck. This is also the case for <a href="https://github.com/pholser/junit-quickcheck" target="_blank">JUnit-Quickcheck</a>, the tool that will be used in the rest of this blog post. As the name suggests, it is a tool for the Java language, based on JUnit.

**Our system under test: the Calculator class**  
For the examples in this blog post, we will use the same Calculator class that was used when we talked about <a href="http://www.ontestautomation.com/an-introduction-to-mutation-testing-and-pit/" target="_blank">mutation testing</a>. In particular, we are going to perform property-based testing on the _add()_ method of this simple calculator:

<pre class="brush: java; gutter: false">public class Calculator {

	int valueDisplayed;

	public Calculator() {
		this.valueDisplayed = 0;
	}

	public void add(int x) {
		this.valueDisplayed += x;
	}

	public int getResult() {
		return this.valueDisplayed;
	}
}</pre>

An example-based unit test for the _add()_ method of this admittedly very basic calculator could look like this:

<pre class="brush: java; gutter: false">@Test
public void testAdditionExampleBased() {
		
	Calculator calculator = new Calculator();
	calculator.add(2);
	assertEquals(calculator.getResult(), 2);		
}</pre>

Using JUnit-Quickcheck, we can replace this example-based unit test to a property-based test as follows:

<pre class="brush: java; gutter: false">@Property(trials = 5)
public void testAddition(int number) {
		
	System.out.println("Generated number for testAddition: " + number);
		
	Calculator calculator = new Calculator();
	calculator.add(number);
	assertEquals(calculator.getResult(), number);
}</pre>

The _@Property_ annotation defines this test as a property-based test, while the _@RunWith_ class-level annotation defines that these tests are to be run using JUnit-Quickcheck. Note that you can mix example-based and property-based tests in the same test class. As long as you run your test class using JUnit, all public void no-parameter methods annotated with _@Test_ will be run just as plain JUnit would do, while all tests annotated with _@Property_ will be run as property-based tests.

The _trials = 5_ attribute tells JUnit-Quickcheck to generate 5 random parameter values (also known as samples). Default is 100. The _System.out.println_ call is used to write the generated parameter value to the console, purely for demonstrational purposes:

<a href="http://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/pbt_basic/" rel="attachment wp-att-1287"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_basic.png" alt="Output of basic JUnit-Quickcheck property" width="420" height="103" class="aligncenter size-full wp-image-1287" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_basic.png 420w, https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_basic-300x74.png 300w" sizes="(max-width: 420px) 100vw, 420px" /></a>

As you can see, JUnit-Quickcheck randomly generated integer values and performed the check using each of these values. The property passes, telling us that our _add()_ method seems to work quite well, even with quite large or negative integers. This is information you wouldn&#8217;t get when using purely example-based tests. Sweet!

**Constraining property values**  
Purely random integers seem to work well for our simple _add()_ method, but obviously there are cases where you want to define some sort of constraints on the values generated by your property-based testing tool. JUnit-Quickcheck provides a number of options to do so:

_1. Using the JUnit <a href="http://junit.sourceforge.net/javadoc/org/junit/Assume.html" target="_blank">Assume</a> class_  
Using Assume, you can define assumptions on the values generated by JUnit-Quickcheck. For example, if you only want to test your _add()_ method using positive integers, you could do this:

<pre class="brush: java; gutter: false">@Property(trials = 5)
public void testAdditionUsingAssume(int number) {
		
	assumeThat(number, greaterThan(0));
		
	System.out.println("Generated number for testAdditionUsingAssume: " + number);
		
	Calculator calculator = new Calculator();
	calculator.add(number);
	assertEquals(calculator.getResult(), number);
}</pre>

When you run these tests, you can see that values generated by JUnit-Quickcheck that do not satisfy the assumption (in this case, three of them) are simply discarded:

<a href="http://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/pbt_assume/" rel="attachment wp-att-1290"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_assume.png" alt="Output of JUnit-Quickcheck using Assume" width="509" height="42" class="aligncenter size-full wp-image-1290" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_assume.png 509w, https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_assume-300x25.png 300w" sizes="(max-width: 509px) 100vw, 509px" /></a>

_2. Using the @InRange annotation_ With this annotation, you can actually constrain the values that are generated by JUnit-Quickcheck:

<pre class="brush: java; gutter: false">@Property(trials = 5)
public void testAdditionUsingInRange(@InRange(minInt = 0) int number) {
		
	System.out.println("Generated number for testAdditionUsingInRange: " + number);
		
	Calculator calculator = new Calculator();
	calculator.add(number);
	assertEquals(calculator.getResult(), number);
}</pre>

Contrary to the approach that uses the Assume class, where values generated by JUnit-Quickcheck are filtered post-generation, when you&#8217;re using the _@InRange_ approach you will always end up with the required number of samples:

<a href="http://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/pbt_inrange/" rel="attachment wp-att-1291"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_inrange.png" alt="Output of JUnit-Quickcheck using InRange" width="523" height="102" class="aligncenter size-full wp-image-1291" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_inrange.png 523w, https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_inrange-300x59.png 300w" sizes="(max-width: 523px) 100vw, 523px" /></a>

_3. Using constraint expressions_  
Here, just like when you&#8217;re using the Assume approach, values generated by JUnit-Quickcheck are filtered after generation by using a _satisfies_ predicate:

<pre class="brush: java; gutter: false">@Property(trials = 5)
public void testAdditionUsingSatisfies(@When(satisfies = "#_ &gt;= 0") int number) {
				
	System.out.println("Generated number for testAdditionUsingSatisfies: " + number);
		
	Calculator calculator = new Calculator();
	calculator.add(number);
	assertEquals(calculator.getResult(), number);
}</pre>

The difference is that when the discard ratio (the percentage of generated values that do not satisfy the constraints defined) exceeds a certain threshold (0.5 by default), the property fails:

<a href="http://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/pbt_satisfies/" rel="attachment wp-att-1292"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies.png" alt="Output of JUnit-Quickcheck using the satisfies predicate" width="537" height="40" class="aligncenter size-full wp-image-1292" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies.png 537w, https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies-300x22.png 300w" sizes="(max-width: 537px) 100vw, 537px" /></a>

<a href="http://www.ontestautomation.com/an-introduction-to-property-based-testing-with-junit-quickcheck/pbt_satisfies_error/" rel="attachment wp-att-1293"><img src="http://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies_error.png" alt="Error generated by JUnit-Quickcheck when discard threshold is not met" width="789" height="314" class="aligncenter size-full wp-image-1293" srcset="https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies_error.png 789w, https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies_error-300x119.png 300w, https://www.ontestautomation.com/wp-content/uploads/2016/03/pbt_satisfies_error-768x306.png 768w" sizes="(max-width: 789px) 100vw, 789px" /></a>

Depending on your preferences and project requirements, you can select any of these strategies for constraining your property values.

**Additional JUnit-Quickcheck features**  
JUnit-Quickcheck comes with some other useful features as well:

  * <a href="http://pholser.github.io/junit-quickcheck/site/0.6-beta-1/usage/seed.html" target="_blank">Fixing the seed</a> used to generate the random property values. You can use this to have JUnit-Quickcheck generate the same values for each and every test run. You may want to use this feature when a property fails, so that you can test the property over and over again with the same set of generated values that caused the failure in the first place.
  * When a property fails for a given set of values, JUnit-Quickcheck will attempt to find smaller sets of values that also fail the property, a technique called shrinking. See <a href="http://pholser.github.io/junit-quickcheck/site/0.6-beta-1/usage/shrinking.html" target="_blank">the JUnit-Quickcheck documentation</a> for an example.

**Download an example project**  
You can download a Maven project containing the Calculator class and all JUnit-Quickcheck tests that have been demonstrated in this post <a href="http://www.ontestautomation.com/files/PropertyBasedTestingDemo.zip" target="_blank">here</a>. Happy property-based testing!