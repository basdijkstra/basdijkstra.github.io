---
id: 485
title: 'Up and running with: Mockito'
date: 2014-06-13T11:52:44+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=485
permalink: /up-and-running-with-mockito/
categories:
  - Test automation tools
tags:
  - java
  - mockito
  - object mocking
  - stubs
  - up and running
---
_This is the third article in our series on new, popular or otherwise interesting tools used in test automation. You can read all posts within this series by clicking [here](http://www.ontestautomation.com/tag/up-and-running/)._

**What is Mockito?**  
Mockito is an open source testing framework for Java that allows developers to create mock objects for use in automated unit tests. It is often used in the Test Driven Development and Behavior Driven Development approaches. Using Mockito mock objects, developers can mock external dependencies and ensure their code interacts with it in the expected manner.

**Where can I get Mockito?**  
Mockito can be downloaded from [this site](https://code.google.com/p/mockito/).

**How do I install and configure Mockito?**  
As the Mockito framework consists of just a single .jar file, all you need to do is to add this .jar as a dependency to your Java project and you&#8217;re ready to start mocking!

**Creating a first Mockito mock object**  
Before we can start creating mock objects using Mockito, we first need a Java class for which we can mock the behavior. To illustrate the workings of Mockito I created a simple class _Login_, consisting of a single constructor, two String variables _username_ and _password_ and a single method _login(username,password)_. The class implementation is included in the download link at the end of this post.

Now, assume that this _Login_ class is either not readily available when you want to perform unit tests that have this class as a dependency, or that you want to emulate a certain type of behavior. To do so, let&#8217;s create a Mockito mock for the _Login_ class:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static Login createLoginMock() {

	// create and return a mock instance of the Login class	
	return mock(Login.class);
}</pre>

Easy, right? Now, let&#8217;s see what we can do with this mock.

**Using your Mockito mock**  
First, let&#8217;s check whether we can interact with the methods of our mock correctly. Mockito provides a _verify_ function for this:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runVerifyTestWithMockito() {
		
	// create mock instance of the login class
	Login loginMock = createLoginMock();
		
	// call methods on the mock instance
	loginMock.setUsername("bas");
	loginMock.setPassword("ok");
		
	// verify that methods are called properly
	verify(loginMock).setUsername("bas");
	verify(loginMock).setPassword("ok");
		
	// attempt to verify a method that hasn&#039;t been called (this generates an error)
	verify(loginMock).getUsername();	
}</pre>

The last call generated an error, as we try to verify an interaction that hasn&#8217;t taken place.

Now, let&#8217;s use Mockito to stub the behavior of a method of the _Login_ class, so that we can use our mock to simulate specific behavior in our unit tests. In Mockito, this is done using a _when &#8230; thenReturn &#8230;_ construction:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runStubTestWithMockito() {
		
	// create mock instance of the login class
	Login loginMock = createLoginMock();
		
	// stub mock behavior
	when(loginMock.login()).thenReturn("login_ok");
		
	// call mock method and verify result
	Assert.assertEquals("login_ok",loginMock.login());
}</pre>

Finally, we can also have a class method of our mock return an exception, even when the actual class implementation might not always do that. This is useful when testing exception handling in your code and is done using a _when &#8230; thenThrow &#8230;_ construction:

<pre class="brush: java; gutter: false; first-line: 1; highlight: []; html-script: false">public static void runExceptionTestWithMockito() {

	// create mock instance of the login class
	Login loginMock = createLoginMock();

	// have the mock return an Exception
	when(loginMock.getPassword()).thenThrow(new RuntimeException());

	// call the mock method that throws the exception
	loginMock.getPassword();
}</pre>

**Useful features**  
I&#8217;ve showed you some of the basic features of Mockito. However, the framework offers loads more, such as:

  * Verification that a method is invoked exactly once / at least x times / never / &#8230;
  * Verification that methods are invoked in a particular order
  * Explicitly verify that certain mock classes have not been interacted with

An extensive list of Mockito features, along with examples, can be found [here](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html).

**Further reading**  
There is a lot of documentation to be found on Mockito on the web, apart from the reading material which I&#8217;ve linked to above. For example, a nice tutorial, including using Mockito for Android testing, can be found on the site of [Lars Vogel](http://www.vogella.com/tutorials/Mockito/article.html). For those that prefer actual books, Tomek Kaczanowski has written a book on Mockito and TestNG, which can be purchased [here](http://www.amazon.com/Practical-Unit-Testing-TestNG-Mockito/dp/839348930X).

An Eclipse project including my Login class implementation and the tests I&#8217;ve demonstrated above can be downloaded [here](http://www.ontestautomation.com/files/mockitoExample.zip).

Happy mocking!