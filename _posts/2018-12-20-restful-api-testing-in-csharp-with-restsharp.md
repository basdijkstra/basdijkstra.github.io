---
title: 'RESTful API testing in C# with RestSharp'
layout: post
permalink: /restful-api-testing-in-csharp-with-restsharp/
categories:
  - API testing
tags:
  - restsharp
---
Since my [last blog post that involved creating tests at the API level in C#](https://www.ontestautomation.com/extending-my-solution-with-api-testing-capabilities-and-troubles-with-open-source-projects/), I've kept looking around for a library that would fit all my needs in that area. So far, I still haven't found anything more suitable than <a href="http://restsharp.org" target="_blank" rel="noreferrer noopener" aria-label="RestSharp (opens in a new tab)">RestSharp</a>. Also, I've found out that RestSharp is more versatile than I initially thought it was, and that's the reason I thought it would be a good idea to dedicate a blog post specifically to this tool.

The examples I show you in this blog post use the <a rel="noreferrer noopener" aria-label="Zippopotam.us API (opens in a new tab)" href="http://api.zippopotam.us/" target="_blank">Zippopotam.us API</a>, a publicly accessible API that resolves a combination of a country code and a zip code to related location data. For example, when you send an HTTP GET call to `http://api.zippopotam.us/us/90210` (where `us` is a path parameter representing a country code, and `90210` is a path parameter representing a zip code), you'll receive this JSON document as a response:

{% highlight json %}
{
	post code: "90210",
	country: "United States",
	country abbreviation: "US",
	places: [
		{
			place name: "Beverly Hills",
			longitude: "-118.4065",
			state: "California",
			state abbreviation: "CA",
			latitude: "34.0901"
		}
	]
}
{% endhighlight %}

**Some really basic checks**  
RestSharp is available as a NuGet package, which makes it really easy to add to your C# project. So, what does an API test written using RestSharp look like? Let's say that I want to check whether the previously mentioned HTTP GET call to http://api.zippopotam.us/us/90210 returns an HTTP status code 200 OK, this is what that looks like:

{% highlight csharp %}
[Test]
public void StatusCodeTest()
{
    // arrange
    RestClient client = new RestClient("http://api.zippopotam.us");
    RestRequest request = new RestRequest("nl/3825", Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    // assert
    Assert.That(response.StatusCode, Is.EqualTo(HttpStatusCode.OK));
}
{% endhighlight %}

If I wanted to check that the content type specified in the API response header is equal to `application/json`, I could do that like this:

{% highlight csharp %}
[Test]
public void ContentTypeTest()
{
    // arrange
    RestClient client = new RestClient("http://api.zippopotam.us");
    RestRequest request = new RestRequest("nl/3825", Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    // assert
    Assert.That(response.ContentType, Is.EqualTo("application/json"));
}
{% endhighlight %}

**Creating data driven tests**  
As you can see, creating these basic checks is quite straightforward with RestSharp. Since APIs are all about sending and receiving data, it would be good to be able to make these tests data driven. NUnit supports data driven testing through the `TestCase` attribute, and using that together with passing the parameters to the test method is really all that it takes to create a data driven test:

{% highlight csharp %}
[TestCase("nl", "3825", HttpStatusCode.OK, TestName = "Check status code for NL zip code 7411")]
[TestCase("lv", "1050", HttpStatusCode.NotFound, TestName = "Check status code for LV zip code 1050")]
public void StatusCodeTest(string countryCode, string zipCode, HttpStatusCode expectedHttpStatusCode)
{
    // arrange
    RestClient client = new RestClient("http://api.zippopotam.us");
    RestRequest request = new RestRequest($"{countryCode}/{zipCode}", Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    // assert
    Assert.That(response.StatusCode, Is.EqualTo(expectedHttpStatusCode));
}
{% endhighlight %}

When you run the test method above, you'll see that it will run two tests: one that checks that the NL zip code 3825 returns HTTP 200 OK, and one that checks that the Latvian zip code 1050 returns HTTP 404 Not Found (Latvian zip codes are not yet available in the Zippopotam.us API). In case you ever wanted to add a third test case, all you need to do is add another `TestCase` attribute with the required parameters and you're set.

**Working with response bodies**  
So far, we've only written assertions on the HTTP status code and the content type header value for the response. But what if we wanted to perform assertions on the contents of the response body?

Technically, we could parse the JSON response and navigate through the response document tree directly, but that would result in hard to read and hard to maintain code (see for an example [this post](https://www.ontestautomation.com/extending-my-solution-with-api-testing-capabilities-and-troubles-with-open-source-projects/) again, where I convert a specific part of the response to a JArray after navigating to it and then do a count on the number of elements in it. Since you're working with dynamic objects, you also don't have the added luxury of autocomplete, because there's no way your IDE knows the structure of the JSON document you expect in a test.

Instead, I highly prefer deserializing JSON responses to actual objects, or POCOs (Plain Old C# Objects) in this case. The JSON response you've seen earlier in this blog post can be represented by the following `LocationResponse` class:

{% highlight csharp %}
public class LocationResponse
{
    [JsonProperty("post code")]
    public string PostCode { get; set; }
    [JsonProperty("country")]
    public string Country { get; set; }
    [JsonProperty("country abbreviation")]
    public string CountryAbbreviation { get; set; }
    [JsonProperty("places")]
    public List&amp;lt;Place&gt; Places { get; set; }
}
{% endhighlight %}

and the `Place` class inside looks like this:

{% highlight csharp %}
public class Place
{
    [JsonProperty("place name")]
    public string PlaceName { get; set; }
    [JsonProperty("longitude")]
    public string Longitude { get; set; }
    [JsonProperty("state")]
    public string State { get; set; }
    [JsonProperty("state abbreviation")]
    public string StateAbbreviation { get; set; }
    [JsonProperty("latitude")]
    public string Latitude { get; set; }
}
{% endhighlight %}

Using the `JsonProperty` attribute allows me to map POCO fields to JSON document elements without names having to match exactly, which in this case is especially useful since some of the element names contain spaces, which are impossible to use in POCO field names.

Now that we have modeled our API response as a C# class, we can convert an actual response to an instance of that class using the deserializer that's built into RestSharp. After doing so, we can refer to the contents of the response by accessing the fields of the object, which makes for far easier test creation and maintenance:

{% highlight csharp %}
[Test]
public void CountryAbbreviationSerializationTest()
{
    // arrange
    RestClient client = new RestClient("http://api.zippopotam.us");
    RestRequest request = new RestRequest("us/90210", Method.GET);

    // act
    IRestResponse response = client.Execute(request);

    LocationResponse locationResponse =
        new JsonDeserializer().
        Deserialize&amp;lt;LocationResponse&gt;(response);

    // assert
    Assert.That(locationResponse.CountryAbbreviation, Is.EqualTo("US"));
}

[Test]
public void StateSerializationTest()
{
    // arrange
    RestClient client = new RestClient("http://api.zippopotam.us");
    RestRequest request = new RestRequest("us/12345", Method.GET);

    // act
    IRestResponse response = client.Execute(request);
    LocationResponse locationResponse =
        new JsonDeserializer().
        Deserialize&amp;lt;LocationResponse&gt;(response);

    // assert
    Assert.That(locationResponse.Places[0].State, Is.EqualTo("New York"));
}
{% endhighlight %}

So, it looks like I'll be sticking with RestSharp for a while when it comes to my basic C# API testing needs. That is, until I've found a better alternative.

All the code that I've included in this blog post is available on my <a href="https://github.com/basdijkstra/restsharp-examples" target="_blank" rel="noreferrer noopener" aria-label="Github page (opens in a new tab)">Github page</a>. Feel free to clone this project and run it on your own machine to see if RestSharp fits your API testing needs, too.