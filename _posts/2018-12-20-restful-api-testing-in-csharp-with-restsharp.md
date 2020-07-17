---
id: 2396
title: 'RESTful API testing in C# with RestSharp'
date: 2018-12-20T14:30:19+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2396
permalink: /restful-api-testing-in-csharp-with-restsharp/
categories:
  - API testing
tags:
  - restsharp
---
Since my [last blog post that involved creating tests at the API level in C#](https://www.ontestautomation.com/extending-my-solution-with-api-testing-capabilities-and-troubles-with-open-source-projects/), I&#8217;ve kept looking around for a library that would fit all my needs in that area. So far, I still haven&#8217;t found anything more suitable than <a href="http://restsharp.org" target="_blank" rel="noreferrer noopener" aria-label="RestSharp (opens in a new tab)">RestSharp</a>. Also, I&#8217;ve found out that RestSharp is more versatile than I initially thought it was, and that&#8217;s the reason I thought it would be a good idea to dedicate a blog post specifically to this tool.

The examples I show you in this blog post use the <a rel="noreferrer noopener" aria-label="Zippopotam.us API (opens in a new tab)" href="http://api.zippopotam.us/" target="_blank">Zippopotam.us API</a>, a publicly accessible API that resolves a combination of a country code and a zip code to related location data. For example, when you send an HTTP GET call to

<pre class="brush: plain; title: ; notranslate" title="">http://api.zippopotam.us/us/90210
</pre>

(where &#8216;us&#8217; is a path parameter representing a country code, and &#8216;90210&#8217; is a path parameter representing a zip code), you&#8217;ll receive this JSON document as a response:

<pre class="brush: plain; title: ; notranslate" title="">{
	post code: &quot;90210&quot;,
	country: &quot;United States&quot;,
	country abbreviation: &quot;US&quot;,
	places: [
		{
			place name: &quot;Beverly Hills&quot;,
			longitude: &quot;-118.4065&quot;,
			state: &quot;California&quot;,
			state abbreviation: &quot;CA&quot;,
			latitude: &quot;34.0901&quot;
		}
	]
}
</pre>

**Some really basic checks**  
RestSharp is available as a NuGet package, which makes it really easy to add to your C# project. So, what does an API test written using RestSharp look like? Let&#8217;s say that I want to check whether the previously mentioned HTTP GET call to http://api.zippopotam.us/us/90210 returns an HTTP status code 200 OK, this is what that looks like:

<pre class="brush: csharp; title: ; notranslate" title="">[Test]
public void StatusCodeTest()
{
    // arrange
    RestClient client = new RestClient(&quot;http://api.zippopotam.us&quot;);
    RestRequest request = new RestRequest(&quot;nl/3825&quot;, Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    // assert
    Assert.That(response.StatusCode, Is.EqualTo(HttpStatusCode.OK));
}
</pre>

If I wanted to check that the content type specified in the API response header is equal to &#8220;application/json&#8221;, I could do that like this:

<pre class="brush: csharp; title: ; notranslate" title="">[Test]
public void ContentTypeTest()
{
    // arrange
    RestClient client = new RestClient(&quot;http://api.zippopotam.us&quot;);
    RestRequest request = new RestRequest(&quot;nl/3825&quot;, Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    // assert
    Assert.That(response.ContentType, Is.EqualTo(&quot;application/json&quot;));
}
</pre>

**Creating data driven tests**  
As you can see, creating these basic checks is quite straightforward with RestSharp. Since APIs are all about sending and receiving data, it would be good to be able to make these tests data driven. NUnit supports data driven testing through the TestCase attribute, and using that together with passing the parameters to the test method is really all that it takes to create a data driven test:

<pre class="brush: csharp; title: ; notranslate" title="">[TestCase(&quot;nl&quot;, &quot;3825&quot;, HttpStatusCode.OK, TestName = &quot;Check status code for NL zip code 7411&quot;)]
[TestCase(&quot;lv&quot;, &quot;1050&quot;, HttpStatusCode.NotFound, TestName = &quot;Check status code for LV zip code 1050&quot;)]
public void StatusCodeTest(string countryCode, string zipCode, HttpStatusCode expectedHttpStatusCode)
{
    // arrange
    RestClient client = new RestClient(&quot;http://api.zippopotam.us&quot;);
    RestRequest request = new RestRequest($&quot;{countryCode}/{zipCode}&quot;, Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    // assert
    Assert.That(response.StatusCode, Is.EqualTo(expectedHttpStatusCode));
}
</pre>

When you run the test method above, you&#8217;ll see that it will run two tests: one that checks that the NL zip code 3825 returns HTTP 200 OK, and one that checks that the Latvian zip code 1050 returns HTTP 404 Not Found (Latvian zip codes are not yet available in the Zippopotam.us API). In case you ever wanted to add a third test case, all you need to do is add another TestCase attribute with the required parameters and you&#8217;re set.

**Working with response bodies**  
So far, we&#8217;ve only written assertions on the HTTP status code and the content type header value for the response. But what if we wanted to perform assertions on the contents of the response body?

Technically, we could parse the JSON response and navigate through the response document tree directly, but that would result in hard to read and hard to maintain code (see for an example [this post](https://www.ontestautomation.com/extending-my-solution-with-api-testing-capabilities-and-troubles-with-open-source-projects/) again, where I convert a specific part of the response to a JArray after navigating to it and then do a count on the number of elements in it. Since you&#8217;re working with dynamic objects, you also don&#8217;t have the added luxury of autocomplete, because there&#8217;s no way your IDE knows the structure of the JSON document you expect in a test.

Instead, I highly prefer deserializing JSON responses to actual objects, or POCOs (Plain Old C# Objects) in this case. The JSON response you&#8217;ve seen earlier in this blog post can be represented by the following LocationResponse class:

<pre class="brush: csharp; title: ; notranslate" title="">public class LocationResponse
{
    [JsonProperty(&quot;post code&quot;)]
    public string PostCode { get; set; }
    [JsonProperty(&quot;country&quot;)]
    public string Country { get; set; }
    [JsonProperty(&quot;country abbreviation&quot;)]
    public string CountryAbbreviation { get; set; }
    [JsonProperty(&quot;places&quot;)]
    public List&amp;lt;Place&gt; Places { get; set; }
}
</pre>

and the Place class inside looks like this:

<pre class="brush: csharp; title: ; notranslate" title="">public class Place
{
    [JsonProperty(&quot;place name&quot;)]
    public string PlaceName { get; set; }
    [JsonProperty(&quot;longitude&quot;)]
    public string Longitude { get; set; }
    [JsonProperty(&quot;state&quot;)]
    public string State { get; set; }
    [JsonProperty(&quot;state abbreviation&quot;)]
    public string StateAbbreviation { get; set; }
    [JsonProperty(&quot;latitude&quot;)]
    public string Latitude { get; set; }
}
</pre>

Using the JsonProperty attribute allows me to map POCO fields to JSON document elements without names having to match exactly, which in this case is especially useful since some of the element names contain spaces, which are impossible to use in POCO field names.

Now that we have modeled our API response as a C# class, we can convert an actual response to an instance of that class using the deserializer that&#8217;s built into RestSharp. After doing so, we can refer to the contents of the response by accessing the fields of the object, which makes for far easier test creation and maintenance:

<pre class="brush: csharp; title: ; notranslate" title="">[Test]
public void CountryAbbreviationSerializationTest()
{
    // arrange
    RestClient client = new RestClient(&quot;http://api.zippopotam.us&quot;);
    RestRequest request = new RestRequest(&quot;us/90210&quot;, Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    LocationResponse locationResponse =
        new JsonDeserializer().
        Deserialize&amp;lt;LocationResponse&gt;(response);

    // assert
    Assert.That(locationResponse.CountryAbbreviation, Is.EqualTo(&quot;US&quot;));
}

[Test]
public void StateSerializationTest()
{
    // arrange
    RestClient client = new RestClient(&quot;http://api.zippopotam.us&quot;);
    RestRequest request = new RestRequest(&quot;us/12345&quot;, Method.GET);

    // act
    IRestResponse response = client.Execute(request);
    LocationResponse locationResponse =
        new JsonDeserializer().
        Deserialize&amp;lt;LocationResponse&gt;(response);

    // assert
    Assert.That(locationResponse.Places[0].State, Is.EqualTo(&quot;New York&quot;));
}
</pre>

So, it looks like I&#8217;ll be sticking with RestSharp for a while when it comes to my basic C# API testing needs. That is, until I&#8217;ve found a better alternative.

All the code that I&#8217;ve included in this blog post is available on my <a href="https://github.com/basdijkstra/restsharp-examples" target="_blank" rel="noreferrer noopener" aria-label="Github page (opens in a new tab)">Github page</a>. Feel free to clone this project and run it on your own machine to see if RestSharp fits your API testing needs, too.