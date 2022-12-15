---
title: Parameterizing tests in RestAssured.Net
layout: post
permalink: /parameterizing-tests-in-rest-assured-net/
categories:
  - RestAssured.Net
tags:
  - csharp
  - api-testing
  - development
---
In a series of (short) blog posts, I would like to share with you some examples of how to use [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} for writing tests for RESTful and GraphQL APIs.

* [Getting started and basic examples](/getting-started-with-rest-assured-net/)
* Parameterized tests (**this blog post**)
* [(De-)serialization](/deserializing-json-and-xml-in-rest-assured-net/)
* Authentication and reuse
* Testing GraphQL APIs

All examples that you see in this blog post series [can be found on GitHub](https://github.com/basdijkstra/rest-assured-net-examples){:target="_blank"}.

### Parameterizing tests
APIs are commonly used to transfer data between systems, or between components in the same system. They also often expose business logic implemented in one component or service to another component or service.

As a result, testing APIs and their implementation with a certain amount of coverage often requires invoking the same endpoint multiple times, with different input values and expected output values.

The naive approach to writing these tests would be to simply copy-paste one test to another and changing the hardcoded values as the test requires. This, however, results in a lot of duplication and a test suite that is hard to read and maintain.

A better option would be to create what is typically known as a parameterized test, also referred to as a data-driven test. In this blog post, I'll show you how to do exactly that with RestAssured.Net.

### Specifying query parameters
APIs take data in various ways. In the examples in this blog post, we'll focus on query and path parameters. In the next blog post, we'll take a closer look at sending data in request payloads.

There are several ways to specify query parameters and their values in your request with RestAssured.Net, as, in the end, it is simply a matter of appending key-value pairs in the right formatting to a string endpoint. You could do this with string concatenation or string interpolation if you'd like. 

However, RestAssured.Net also offers the `QueryParam()` utility method to make adding query parameters and their values to your endpoint a little easier, as can be seen in this example:

{% highlight csharp %}
[Test]
public void QueryParameterExample()
{
    Given()
    .QueryParam("text", "RestAssured.Net")
    .When()
    .Get("http://md5.jsontest.com")
    .Then()
    .Body("$.original", NHamcrest.Is.EqualTo("RestAssured.Net"))
    .Body("$.md5", NHamcrest.Is.EqualTo("3d0761128d4c535dd4ee69b54abde303"));
}
{% endhighlight %}

The endpoint that will be invoked in this test is `http://md5.jsontest.com?text=RestAssured.Net`.

To specify multiple query parameters, you can call `QueryParam()` multiple times, or pass a Dictionary with query parameter names and values to the `QueryParams()` method. More examples on using query parameters can be found in [the RestAssured.Net usage guide](https://github.com/basdijkstra/rest-assured-net/wiki/Usage-Guide#specifying-query-parameters){:target="_blank"}.

### Specifying path parameters
The same principle can be used when specifying path parameters as well. Next to string interpolation and string concatenation, RestAssured.Net offers the `PathParam()` method to specify the value for a path parameter.

{% highlight csharp %}
[Test]
public void PathParameterExample()
{
    Given()
    .PathParam("countryCode", "us")
    .PathParam("zipCode", 90210)
    .When()
    .Get("http://api.zippopotam.us/{{countryCode}}/{{zipCode}}")
    .Then()
    .Body("$.places[0].['place name']", NHamcrest.Is.EqualTo("Beverly Hills"));
}
{% endhighlight %}

The endpoint that will be invoked in this test is `http://api.zippopotam.us/us/90210`.

With path parameters, only the value appears in the endpoint. The path parameter name can be chosen arbitrarily, but keep in mind that descriptive names definitely add to the readability of your test.

RestAssured.Net will then look for a placeholder (identified by the 'double handlebar' notation) with the same name in the endpoint, and will replace the placeholder with the value specified in the `PathParam()` method.

As you can see, you can add more than one path parameter in your test. You can also use `PathParams()` to provide a dictionary of path parameters in a single method call. More examples on using path parameters can be found in [the RestAssured.Net usage guide](https://github.com/basdijkstra/rest-assured-net/wiki/Usage-Guide#specifying-path-parameters){:target="_blank"}. 

### Creating our parameterized test
Now that we have added parameter definitions to our test, we can combine that with the capability our test runner provides to create a parameterized test.

In this example, I use NUnit, but most other test runners I know (in C#, but also in other languages) support this concept as well. In NUnit, we can supply our test cases using the `[TestCase]` annotation like this:

{% highlight csharp %}
[TestCase("us", 90210, "Beverly Hills", TestName = "US zip code 90210 maps to Beverly Hills")]
[TestCase("ca", "Y1A", "Whitehorse", TestName = "CA zip code Y1A maps to Whitehorse")]
[TestCase("it", 50123, "Firenze", TestName = "IT zip code 50123 maps to Firenze")]
public void ParameterizedTestExample(string countryCode, object zipCode, string expectedPlace)
{
    Given()
    .PathParam("countryCode", countryCode)
    .PathParam("zipCode", zipCode)
    .When()
    .Get("http://api.zippopotam.us/{{countryCode}}/{{zipCode}}")
    .Then()
    .Body("$.places[0].['place name']", NHamcrest.Is.EqualTo(expectedPlace));
}
{% endhighlight %}

As you can see, we pass in the values that we want to use in our test as arguments to our test method, and then replace the previously hardcoded values in the test method body with these arguments. NUnit will then run this method three times, once for each `[TestCase]` we defined.

An alternative to using the `[TestCase]` option that scales to more test cases and allows you to use external data sources (or apply some sort of logic to calculate or derive the values) is by means of the `[TestCaseSource]` attribute. This feature is documented [here](https://docs.nunit.org/articles/nunit/writing-tests/attributes/testcasesource.html){:target="_blank"}, and an example using RestSharp can be found [here](/data-driven-testing-in-c-with-nunit-and-restsharp/). 

That's it for working with parameters and creating parameterized tests in RestAssured.Net. In the next blog post in this series, we'll look at efficient creation of request payloads and processing of response payloads by means of (de-)serialization.