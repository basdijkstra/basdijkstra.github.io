---
id: 1835
title: Extending my solution with API testing capabilities, and troubles with open source projects
date: 2017-04-04T09:15:25+02:00
author: Bas
layout: revision
guid: http://www.ontestautomation.com/1834-revision-v1/
permalink: /1834-revision-v1/
---
In <a href="http://www.ontestautomation.com/how-i-would-approach-creating-automated-user-interface-driven-tests/" target="_blank">last week&#8217;s blog post</a>, I introduced how I would approach creating a solution for creating and executing automated user interface-driven tests. In this blog post, I&#8217;m going to extend the capabilities of <a href="https://github.com/basdijkstra/ota-solution" target="_blank">my solution</a> by adding automated tests that exercise a RESTful API.

Returning readers might know that I&#8217;m a big fan of <a href="http://rest-assured.io/" target="_blank">REST Assured</a>. However, since that&#8217;s a Java-based DSL, and my solution is written in C#, I can&#8217;t use REST Assured. I&#8217;ve spent some time looking at alternatives and decided upon using <a href="http://restsharp.org/" target="_blank">RestSharp</a> for this example. Why RestSharp? Because it has a clean and fairly readable API, which makes it easy to use, even for non-wizardlike programmers, such as yours truly. This is a big plus for me when creating test automation solutions, because, as a consultant, there will always come a moment where you need to hand over your solution to somebody else. And that somebody might be just as inexperienced when it comes to programming as myself, so I think it&#8217;s important to use tools that are straightforward and easy to use while still powerful enough to perform the required tasks. RestSharp ticks those boxes fairly well.

**A sample feature and scenario for a RESTful API**  
Again, we start with the top level of our test implementation: the feature and scenario(s) that describe the required behaviour. Here goes:

<pre class="brush: text; gutter: false">Feature: CircuitsApi
	In order to impress my friends
	As a Formula 1 fan
	I want to know the number of races for a given Formula 1 season

@api
Scenario Outline: Check the number of races in a season
	Given I want to know the number of Formula One races in &lt;season&gt;
	When I retrieve the circuit list for that season
	Then there should be &lt;numberOfCircuits&gt; circuits in the list returned
	Examples:
	| season | numberOfCircuits |
	| 2017   | 20               |
	| 2016   | 21               |
	| 1966   | 9                |
	| 1950   | 8                |</pre>

Note that I&#8217;ve added a tag _@api_ to the scenario. This is so I can prevent my solution from starting a browser for these API tests as well (which would just slow down test execution) by writing dedicated setup and teardown methods that execute only for scenarios with a certain tag. This can be done real easy with SpecFlow. See <a href="https://github.com/basdijkstra/ota-solution" target="_blank">the GitHub page for the solution</a> for more details.

**The step definitions**  
So, how are the above scenario steps implemented? In the Given step, I handle creating the _RestClient_ that is used to send the HTTP request and intercept the response, as well as setting the path parameter specifying the specific year for which I want to check the number of races:

<pre class="brush: csharp; gutter: false">private RestClient restClient;
private RestRequest restRequest;
private IRestResponse restResponse;

[Given(@"I want to know the number of Formula One races in (.*)")]
public void GivenIWantToKnowTheNumberOfFormulaOneRacesIn(string season)
{
    restClient = new RestClient(Constants.ApiBaseUrl); //http://ergast.com/api/f1

    restRequest = new RestRequest("{season}/circuits.json", Method.GET);

    restRequest.AddUrlSegment("season", season);
}</pre>

The When step is even more straightforward: all is done here is executing the _RestClient_ and storing the response in the _IRestResponse_:

<pre class="brush: csharp; gutter: false">[When(@"I retrieve the circuit list for that season")]
public void WhenIRetrieveTheCircuitListForThatSeason()
{
    restResponse = restClient.Execute(restRequest);
}</pre>

Finally, in the Then step, we parse the response to get the JSON field value we&#8217;re interested in and check whether it matches the expected value. In this case, we&#8217;re not interested in a field value, though, but rather in the number of times a field appears in the response (in this case, the length of the array of circuits). And, obviously, we want to report the result of our check to the ExtentReports report we&#8217;re creating during test execution:

<pre class="brush: csharp; gutter: false">[Then(@"there should be (.*) circuits in the list returned")]
public void ThenThereShouldBeCircuitsInTheListReturned(int numberOfSeasons)
{
    dynamic jsonResponse = JsonConvert.DeserializeObject(restResponse.Content);

    JArray circuitsArray = jsonResponse.MRData.CircuitTable.Circuits;

    OTAAssert.AssertEquals(null, test, circuitsArray.Count, numberOfSeasons, "The actual number of circuits in the list is equal to the expected value " + numberOfSeasons.ToString());
}</pre>

Basically, what we&#8217;re doing here is deserializing the JSON response and storing it into a dynamic object. I wasn&#8217;t familiar with the _dynamic_ concept before, but it turns out to be very useful here. The dynamic type can be used for objects of which you don&#8217;t know the structure until runtime, which holds true here (we don&#8217;t know what the JSON response looks like). Then, we can simply traverse the dynamic _jsonResponse_ until we get to the field we need for our check. It may not be the best or most reusable solution, but it definitely shows the power of the C# language here.

**The trouble with RestSharp**  
As you can see, with RestSharp, it&#8217;s really easy to write tests for RESTful APIs and add them to our solution. There&#8217;s one problem though, and that&#8217;s that RestSharp no longer seems to be actively maintained. The most recent version of RestSharp was released on August 26 of 2015, more than a year and a half ago. There&#8217;s no response to the issues posted on GitHub, either, which also doesn&#8217;t bode very well for the liveliness of the project. For me, when deciding whether or not to use an open source project, this is a big red flag.

One alternative to RestSharp I found was <a href="https://github.com/lamchakchan/RestAssured.Net" target="_blank">RestAssured.Net</a>. This project looks like an effort to port the original REST Assured to C#. It looks useful enough, however, it suffers from the same problem that RestSharp does: no activity. For me, that&#8217;s enough reason to discard it.

Just before writing this post, I was made aware of yet another possible solution, called <a href="http://tmenier.github.io/Flurl/" target="_blank">Flurl</a>. This does look like a promising alternative, but unfortunately I didn&#8217;t have the time to try it out for myself before the due date of this blog post. I&#8217;ll check it out during the week and if it lives up to its promising appearance, Flurl stands a good chance of being the topic for next week&#8217;s blog post. Until then, you can find my RestSharp implementation of the RESTful API tests on <a href="https://github.com/basdijkstra/ota-solution" target="_blank">the GitHub page of my solution</a>.