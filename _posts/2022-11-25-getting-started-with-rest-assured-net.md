---
title: Getting started with RestAssured.Net
layout: post
permalink: /getting-started-with-rest-assured-net/
categories:
  - RestAssured.Net
tags:
  - csharp
  - api-testing
  - development
---
In a series of (short) blog posts, I would like to share with you some examples of how to use [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} for writing tests for RESTful and GraphQL APIs.

* Getting started and basic examples (**this blog post**)
* [Parameterized tests](/parameterizing-tests-in-rest-assured-net/)
* [(De-)serialization](/deserializing-json-and-xml-in-rest-assured-net/)
* Authentication and reuse
* Testing GraphQL APIs

All examples that you see in this blog post series [can be found on GitHub](https://github.com/basdijkstra/rest-assured-net-examples){:target="_blank"}.

### Background
I started developing this library for two reasons.

1. I was looking for a way to improve my C# development skills.
2. I couldn't find a decent library to use when writing tests for REST APIs in C#. While RestSharp is a wonderful library, writing tests using RestSharp sometimes feels overly complex.

I've been a fan of the [Java REST Assured library](https://rest-assured.io/){:target="_blank"} for years, and came up with the idea to port it to C#. Johan, the creator of REST Assured, was kind enough to give me permission to use the name and draw inspiration from the DSL of the 'original' REST Assured, and he even gave me some valuable tips and advice, too. Thank you once again!

### Getting started
Getting started with RestAssured.Net is simple:

1. Create a new test project (the examples you see in this blog post series use NUnit)
2. Add RestAssured.Net as a dependency through NuGet
3. Add the `using static RestAssured.Dsl;` directive to your test class
4. Write your tests!

For those of you who are familiar with REST Assured in Java, the way to make an HTTP GET call to an endpoint and check the response status code (what I call the 'Hello, World!' of API testing) should look pretty familiar:

{% highlight csharp %}
[Test]
public void GetLocationsForUsZipCode90210_CheckStatusCode_ShouldBe200()
{
    Given()
    .When()
    .Get("http://api.zippopotam.us/us/90210")
    .Then()
    .StatusCode(200);
}
{% endhighlight %}

As you can see, in a single, readable line of code, you can create and perform an HTTP call and receive and verify the response. To log the response details to the console, you can do this:

{% highlight csharp %}
[Test]
public void GetLocationsForUsZipCode90210_LogResponseDetails()
{
    Given()
    .When()
    .Get("http://api.zippopotam.us/us/90210")
    .Then()
    .Log().All();
}
{% endhighlight %}

which yields the following output, including status code, headers, response body and response time:

```
HTTP 200 (OK)
Date: Fri, 25 Nov 2022 15:36:42 GMT
Connection: keep-alive
X-Cache: hit
charset: UTF-8
Vary: Accept-Encoding
CF-Cache-Status: DYNAMIC
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v3?s=F%2B9%2Fc5yMtrMtkXrp2iRyG5%2BTMDQcFeUB4JOrSc0ABXVhONkyOZ3v7YaEIbJvYf8%2BISAuIB1hcqM6%2FXduvAewxDqpJgvmSaGELqH6hGYit7Xg86VKOdHNQQvQY0kd0h90T4wcu35VhRKaeztSyd7AAQ%3D%3D"}],"group":"cf-nel","max_age":604800}
NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
Access-Control-Allow-Origin: *
Server: cloudflare
CF-RAY: 76fb761d6b090bce-AMS
Alt-Svc: h3=":443", h3-29=":443"
{
  "post code": "90210",
  "country": "United States",
  "country abbreviation": "US",
  "places": [
    {
      "place name": "Beverly Hills",
      "longitude": "-118.4065",
      "state": "California",
      "state abbreviation": "CA",
      "latitude": "34.0901"
    }
  ]
}
Response time: 236,8491 ms
```

RestAssured.Net also gives you the ability to verify response headers, and comes with utility methods to verify the `Content-Type` response header. More details about how to verify response headers can be found [here](https://github.com/basdijkstra/rest-assured-net/wiki/Usage-Guide#header-values){:target="_blank"}.

### Verifying response body elements
To verify JSON response body elements, we need to specify two things: a JsonPath expression that extracts the element(s) to verify, and an expectation about their value. Here's how to extract a single element value and check that it is an exact match with a predefined value:

{% highlight csharp %}
[Test]
public void GetLocationsForUsZipCode90210_CheckCountry_ShouldBeUnitedStates()
{
    Given()
    .When()
    .Get("http://api.zippopotam.us/us/90210")
    .Then()
    .Body("$.country", NHamcrest.Is.EqualTo("United States"));
}
{% endhighlight %}

As you can see, RestAssured.Net uses [NHamcrest](https://github.com/nhamcrest/NHamcrest){:target="_blank"} to express the expectations about response property values. This gives a lot of flexibility with regards to the specific expectations that you might have around specific response properties, including status codes and header and body values.

A slightly more complex example can be seen when we verify that a list of place names contains an exact match for a specific place name:

{% highlight csharp %}
[Test]
public void GetLocationsForDeZipCode24848_CheckPlaces_ShouldContainKropp()
{
    Given()
    .When()
    .Get("http://api.zippopotam.us/de/24848")
    .Then()
    .Body("$.places[*].['place name']", NHamcrest.Has.Item(NHamcrest.Is.EqualTo("Kropp")));
}
{% endhighlight %}

In the next blog post in this series, we'll look at specifying query and path parameters, and how to use that to create parameterized tests using RestAssured.Net.