---
id: 1856
title: Let your test automation talk to you
date: 2017-05-02T09:50:41+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1854-revision-v1/
permalink: /1854-revision-v1/
---
Most of my current projects involve me paving the automation way by discussing requirements and needs with stakeholders, deciding on an approach and the tools used, setting up a solution and some initial tests and then instructing others and handing over the project. I love doing this type of projects, as it allows me (who&#8217;s bored quite easily and quickly) to move from project to project and from client to client regularly (sometimes once every couple of weeks). One of the most important factors in facilitating an easy handover of &#8216;my&#8217; (it isn&#8217;t really mine, of course) automation solution to those who are going to continue and expand on it is by making the automation setup as self-explanatory as possible.

This is especially so when those that are going to take over don&#8217;t have as much experience with the tools and architecture that has been decided upon (or, in some cases, that I&#8217;ve decided to use). Let&#8217;s take a look at two ways to make it easy to hand over automation (results) to your successor (stakeholder), or, as I&#8217;d like to call it, to have your automation talk to you about what it&#8217;s doing and what the results of executing the automated tests are. Not literally talk to you (though that would be nice!), but you know what I mean..

**Through the code**  
One of the most effective ways of making your automation solution easily explainable to others is through the code. Not only should your code be well structured and maintainable, good code requires little to no comments (who&#8217;s got time or motivation to write comments anyway?) because it is self-explanatory. Some prime examples of self describing code that I&#8217;ve used or seen are:

_Having your Page Object methods in Selenium return Page Objects._  
When you write your Page Object Methods like this:

<pre class="brush: csharp; gutter: false">public LoginPage SetEmailAddressTo(string emailAddress)
{
    PElements.SendKeys(_driver, textfieldEmailAddress, emailAddress);
    return this;
}

public LoginPage SetPasswordTo(string password)
{
    PElements.SendKeys(_driver, textfieldPassword, password);
    return this;
}

public void ClickLoginButton()
{
    PElements.Click(_driver, buttonLogin);
}</pre>

you can write your tests (or your step definition implementations, if you&#8217;re using Cucumber or SpecFlow) like this:

<pre class="brush: csharp; gutter: false">[When(@"he logs in using his credentials")]
public void WhenHeLogsInUsingHisCredentials()
{
    new LoginPage().
        SetEmailAddressTo("user@example.com").
        SetPassword("mypassword").
        ClickLoginButton();
}</pre>

It&#8217;s instantly clear what your tests is doing here, all by means of allowing method chaining through well-chosen method return types and expressive method names.

_Choosing to work with libraries that offer fluent APIs_  
Two prime examples I often use are <a href="http://rest-assured.io/" target="_blank">REST Assured</a> and <a href="http://wiremock.org/" target="_blank">WireMock</a>. For example, REST Assured allows you to create powerful, yet highly readable tests for RESTful APIs, while abstracting away boilerplate code like this:

<pre class="brush: java; gutter: false">@Test
public void verifyCountryForZipCode() {
						
	given().
	when().
		get("http://localhost:9876/us/90210").
	then().
		assertThat().
		body("country", equalTo("United States"));
}</pre>

WireMock does something similar, but for creating over-the-wire mocks:

<pre class="brush: java; gutter: false">public void setupExampleStub() {

	stubFor(post(urlEqualTo("/pingpong"))
		.withRequestBody(matching("&lt;input&gt;PING&lt;/input&gt;"))
		.willReturn(aResponse()
			.withStatus(200)
			.withHeader("Content-Type", "application/xml")
			.withBody("&lt;output&gt;PONG&lt;/output&gt;")));
}</pre>

One of the main reasons I like to work with both is that it&#8217;s so easy to read the code and explain to others what it does, without loss of power and features.

_Create fluent assertions_  
Arguably the most important part of your test code is where the assertions are being made. Of course you need readable arrange (given) and act (when) sections too, but when you&#8217;re explaining or demonstrating your code to others, having readable assert (then) sections will be of great help, simply because that&#8217;s where the magic happens (so to say). You can make your assertions pretty much self-explanatory by using libraries such as <a href="http://hamcrest.org/" target="_blank">Hamcrest</a> when you&#8217;re using Java, or <a href="http://fluentassertions.com/" target="_blank">FluentAssertions</a> when you&#8217;re working with C#. The latter even allows you to create better readable error messages when an assertion fails:

<pre class="brush: csharp; gutter: false">IEnumerable collection = new[] { 1, 2, 3 };
collection.Should().HaveCount(4, "because we thought we put four items in the collection"));</pre>

results in the following error message:

<pre class="brush: text; gutter: false">Expected &lt;4&gt; items because we thought we put four items in the collection, but found &lt;3&gt;.</pre>

**Through the reporting**  
Now that you&#8217;ve got your code all cleaned up and readable, it&#8217;s time to shift our attention towards the output of the test execution: your reporting. We&#8217;ve seen the first step towards clearly readable and therefore useful reporting above: readable error messages. But good reporting is more than that. First, you need to think of the audience for your report. Who&#8217;s going to be informed by your report?

  * Is it a developer? In that case you might want to include details about the test data used, steps to reproduce the erroneous situation and detailed information about the failure (such as stack traces and screenshots) in the report. Pretty much everything that makes it easy for your devs to analyze the failure, so to say. In my experience, more is better here.
  * Is it a manager or a product owner? In that case he or she is probably only interested in the overall outcome (did the tests pass?). Maybe also in test coverage (what is it that we covered with these automated tests?). They&#8217;ll probably be less interested in stack traces, though.
  * Is it a build tool, such as Jenkins or Bamboo? In that case the highest priority is that the results are presented in a way that can be interpreted automatically by that tool. A prime example is the xUnit test output format that frameworks such as JUnit and TestNG produce. These can be picked up, parsed and presented by Jenkins, Bamboo and any other CI tool that&#8217;s worth its salt without any additional configuration or programming.

Again, think about the audience for your test automation reports, and include the information that&#8217;s valuable and useful to them. Nothing less, but nothing more either. This might require you to talk with these stakeholders. This might even require you to create more than a single report for every test run. Doesn&#8217;t matter. Have your automation talk to you and to your stakeholders by means of proper reporting.