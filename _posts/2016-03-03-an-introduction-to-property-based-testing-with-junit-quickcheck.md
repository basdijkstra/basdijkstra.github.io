---
id: 1284
title: An introduction to property-based testing with JUnit-Quickcheck
date: 2016-03-03T08:19:12+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=1284
permalink: /an-introduction-to-property-based-testing-with-junit-quickcheck/
categories:
  - General test automation
  - Test automation tools
tags:
  - java
  - junit
  - junit-quickcheck
  - property-based testing
---
Even though I consider having an extensive set of unit and unit integration tests a good thing in almost any case, there is still a fundamental shortcoming with automated checks that I feel should be addressed more often. Almost all checks I come across are very much example-based, meaning that only a single combination of input values is being checked. This goes for unit-, API-level as well as UI-driven tests, by the way.

Of course, this is already far better than not checking anything at all, but how do you make sure that your check passes for all, or at least for a considerable subset of all possible input parameter combinations?

In most cases, it is computationally impossible to perform a check for every combination of all possible input values (have you ever tried to work out what all possible values for a single string input parameter are?), so we need an approach that is able to generate a lot of (preferably random) input values that satisfy a set of predicates, and subsequently verify whether the check holds for all these input values and value combinations.

Enter property-based testing.

#### What is property-based testing?  
In short, property-based testing pretty much exactly addresses the problem described above: based on an existing check, randomly generate a lot of input parameter combinations that satisfy predefined properties and see if the check passes for each of these combinations.

This includes using negative and empty parameter values and other edge cases, all to try and break the system (or prove its robustness, if you want to look at it from the positive side&#8230;). You can do this either manually &#8211; although this does constitute a lot of effort &#8211; or, and given the nature of this blog this is of course the preferred method, use a tool to do the menial work for you.

By the way, Jessica Kerr's blog contains [a great introduction to property-based testing](http://blog.jessitron.com/2013/04/property-based-testing-what-is-it.html){:target="_blank"}. It's this post that first introduced me to the concept.

#### Why should you use property-based testing?  
Again, the answer is in the first paragraph: because property-based testing will give you far more information about the functional correctness and the robustness of your software than executing mere example-based checks. Especially when combined with [mutation testing](/an-introduction-to-mutation-testing-and-pit/), using property-based testing could lead to having a far more powerful and effective test suite.

#### Tool: JUnit-Quickcheck  
So, what tools are available for property-based testing? The archetypical tool is [QuickCheck](https://hackage.haskell.org/package/QuickCheck){:target="_blank"} for the Haskell language. Most other property-based testing tools are somehow derived from QuickCheck.

This is also the case for [JUnit-Quickcheck](https://github.com/pholser/junit-quickcheck){:target="_blank"}, the tool that will be used in the rest of this blog post. As the name suggests, it is a tool for the Java language, based on JUnit.

#### Our system under test: the Calculator class  
For the examples in this blog post, we will use the same Calculator class that was used when we talked about [mutation testing](/an-introduction-to-mutation-testing-and-pit/). In particular, we are going to perform property-based testing on the `add()` method of this simple calculator:

{% highlight java %}
public class Calculator {

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
}
{% endhighlight %}

An example-based unit test for the `add()` method of this admittedly very basic calculator could look like this:

{% highlight java %}
@Test
public void testAdditionExampleBased() {
		
	Calculator calculator = new Calculator();
	calculator.add(2);
	assertEquals(calculator.getResult(), 2);		
}
{% endhighlight %}

Using JUnit-Quickcheck, we can rewrite this example-based unit test to become a property-based test as follows:

{% highlight java %}
@Property(trials = 5)
public void testAddition(int number) {
		
	System.out.println("Generated number for testAddition: " + number);
		
	Calculator calculator = new Calculator();
	calculator.add(number);
	assertEquals(calculator.getResult(), number);
}
{% endhighlight %}

The `@Property` annotation defines this test as a property-based test, while the `@RunWith` class-level annotation defines that these tests are to be run using JUnit-Quickcheck.

Note that you can mix example-based and property-based tests in the same test class. As long as you run your test class using JUnit, all public void no-parameter methods annotated with `@Test` will be run just as plain JUnit would do, while all tests annotated with `@Property` will be run as property-based tests.

The `trials = 5` attribute tells JUnit-Quickcheck to generate 5 random parameter values (also known as samples). Default is 100.

The `System.out.println()` call is used to write the generated parameter value to the console, purely for demonstrational purposes:

![pbt_basic](/images/blog/pbt_basic.png "Output of basic usage of JUnit-QuickCheck")

As you can see, JUnit-Quickcheck randomly generated integer values and performed the check using each of these values. The property passes, telling us that our `add()` method seems to work quite well, even with quite large or negative integers. This is information you wouldn&#8217;t get when using purely example-based tests. Sweet!

#### Constraining property values  
Purely random integers seem to work well for our simple `add()` method, but obviously there are cases where you want to define some sort of constraints on the values generated by your property-based testing tool.

JUnit-Quickcheck provides a number of options to do so:

_Using the JUnit `Assume` class_  

Using [Assume](https://junit.org/junit4/javadoc/latest/index.html){:target="_blank"}, you can define assumptions on the values generated by JUnit-Quickcheck. For example, if you only want to test your `add()` method using positive integers, you could do this:

{% highlight java %}
@Property(trials = 5)
public void testAdditionUsingAssume(int number) {
		
	assumeThat(number, greaterThan(0));
		
	System.out.println("Generated number for testAdditionUsingAssume: " + number);
		
	Calculator calculator = new Calculator();
	calculator.add(number);
	assertEquals(calculator.getResult(), number);
}
{% endhighlight %}

When you run these tests, you can see that values generated by JUnit-Quickcheck that do not satisfy the assumption (in this case, three of them) are simply discarded:

![pbt_assume](/images/blog/pbt_assume.png "Output of JUnit-QuickCheck with Assume")

_Using the `@InRange` annotation_

With this annotation, you can constrain the values that are generated by JUnit-Quickcheck:

{% highlight java %}
@Property(trials = 5)
public void testAdditionUsingInRange(@InRange(minInt = 0) int number) {

    System.out.println("Generated number for testAdditionUsingInRange: " + number);

    Calculator calculator = new Calculator();
    calculator.add(number);
    assertEquals(calculator.getResult(), number);
}
{% endhighlight %}

Contrary to the approach that uses the `Assume` class, where values generated by JUnit-Quickcheck are filtered post-generation, when you're using the `@InRange` approach you will always end up with the required number of samples:

![pbt_inrange](/images/blog/pbt_inrange.png "Output of JUnit-QuickCheck with InRange")

_Using constraint expressions_

Here, just like when you're using the `Assume` approach, values generated by JUnit-Quickcheck are filtered after generation by using a `satisfies` predicate:

{% highlight java %}
@Property(trials = 5)
public void testAdditionUsingSatisfies(@When(satisfies = "#_ &gt;= 0") int number) {

    System.out.println("Generated number for testAdditionUsingSatisfies: " + number);

    Calculator calculator = new Calculator();
    calculator.add(number);
    assertEquals(calculator.getResult(), number);
}
{% endhighlight %}

The difference is that when the discard ratio (the percentage of generated values that do not satisfy the constraints defined) exceeds a certain threshold (0.5 by default), the property fails:

![pbt_satisfies](/images/blog/pbt_satisfies.png "Output of JUnit-QuickCheck using satisfies predicate")

![pbt_satisfies_error](/images/blog/pbt_satisfies_error.png "Output of JUnit-QuickCheck using satisfies predicate - error situation")

Depending on your preferences and project requirements, you can select any of these strategies for constraining your property values.

#### Additional JUnit-Quickcheck features  
JUnit-Quickcheck comes with some other useful features as well:

  * [Fixing the seed](https://pholser.github.io/junit-quickcheck/site/1.0/usage/seed.html){:target="_blank"} used to generate the random property values. You can use this to have JUnit-Quickcheck generate the same values for each and every test run. You may want to use this feature when a property fails, so that you can test the property over and over again with the same set of generated values that caused the failure in the first place.
  * When a property fails for a given set of values, JUnit-Quickcheck will attempt to find smaller sets of values that also fail the property, a technique called [shrinking](https://pholser.github.io/junit-quickcheck/site/1.0/usage/shrinking.html){:target="_blank"}.

All in all, property-based testing is an interesting and powerful technique to improve your example-based tests and increase your coverage. It does require thinking long and hard about what it is that you want to test exactly, and what properties your input and expected output parameters should adhere to. 