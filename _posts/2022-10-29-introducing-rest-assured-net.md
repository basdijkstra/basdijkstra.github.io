---
title: Introducing RestAssured.Net
layout: post
permalink: /introducing-rest-assured-net/
categories:
  - RestAssured.Net
tags:
  - csharp
  - api-testing
  - development
---
As you might know, I am a fan of the REST Assured library for Java, most notably for the ease with which you can use it to write readable yet powerful tests for HTTP-based APIs.

Also, ever since I worked on the [Python](https://github.com/testproject-io/python-opensdk){:target="_blank"} and the [C#](https://github.com/testproject-io/csharp-opensdk){:target="_blank"} SDKs for the [TestProject](https://testproject.io/){:target="_blank"} platform, I've been thinking about creating and publishing an open source library of my own. Why? Partly because I love the open source community, partly because I am a big believer in giving back and 'paying it forward', and partly because I think it would be a valuable experience as well as a good way to practice and hone my development skills.

Combine these two with the fact that while [RestSharp](https://restsharp.dev/){:target="_blank"} is [a very useful library](/data-driven-testing-in-c-with-nunit-and-restsharp/), I've been looking for years for a library that would make it as easy to write tests for APIs in C# as REST Assured makes doing so in Java, what you get is the idea to bring REST Assured _to_ C#.

And that's how **RestAssured.Net** was born.

I just released version 0.4.1 this weekend, which is the first beta release (after a couple of alpha releases), meaning I think it is ready for the testing community to give it a spin. Here are the most important links:

* [The project on GitHub](https://github.com/basdijkstra/rest-assured-net){:target="_blank"}
* [The usage guide](https://github.com/basdijkstra/rest-assured-net/wiki/Usage-Guide){:target="_blank"}
* [The list of currently open issues](https://github.com/basdijkstra/rest-assured-net/issues){:target="_blank"}
* [The package on NuGet](https://www.nuget.org/packages/RestAssured.Net){:target="_blank"}

![rest_assured_net](/images/blog/rest_assured_net_on_nuget.png "RestAssured.Net on NuGet")

Let's have a look at how to use RestAssured.Net to write tests for a REST API.

### Getting started

To start using RestAssured.Net, all you need to do is:

* Create a new C# project using either .NET 6 or .NET Core 3.1 (both LTS versions of the .NET framework)
* Add your favourite unit testing framework
* Add the RestAssured.Net package
* Add the following _using_ directive:

{% highlight csharp %}
using static RestAssuredNet.RestAssuredNet;
{% endhighlight %}

and you're good to go!

### A first test

For those of you familiar with REST Assured in Java, the following syntax is probably very familiar:

{% highlight csharp %}
[Test]
public void CheckResponseStatusCodeAndJsonResponseBodyElementValue()
{
    Given()
    .When()
        .Get("http://api.zippopotam.us/us/90210")
    .Then()
        .AssertThat()
        .StatusCode(200)
    .And()
        .Body("$.places[0].state", NHamcrest.Is.EqualTo("California"));
}
{% endhighlight %}

The Given-When-Then Gherkin syntax nicely divides our test (which is just a single line of code) in three distinct sections: setting up the request (_Given_), performing the HTTP call (_When_) and processing and/or verifying the response (_Then_).

I'm not going to show all the capabilities of RestAssured.Net here, as [the usage guide](https://github.com/basdijkstra/rest-assured-net/wiki/Usage-Guide){:target="_blank"} contains examples for most, if not all of its features, but here's a non-exhaustive list of what's supported:

**For requests**
* adding Content-Type, Accept and custom headers
* adding cookies
* adding either Basic or OAuth2 token-based authorization
* adding query and path parameters
* adding request payloads, with the option to serialize objects to JSON or XML

**For responses**
* verifying header values and JSON response body elements
* extracting entire responses, header values and JSON and XML response body elements
* deserializing response payloads into objects from JSON or XML

To show how straightforward using RestAssured.Net is, here's an example of how that deserialization works, by the way:

{% highlight csharp %}
[Test]
public void DeserializeIntoCSharpObject()
{
    TodoItem todoItem = (TodoItem)Given()
    .When()
        .Get("https://jsonplaceholder.typicode.com/todos/1")
        .As(typeof(TodoItem));
    
    Assert.That(todoItem.title, Is.EqualTo("delectus aut autem"));
}
{% endhighlight %}

This is the implementation of the `TodoItem` class:

{% highlight csharp %}
public class TodoItem
{
    public int userId { get; set; }
    public int id { get; set; }
    public string title { get; set; }
    public bool completed { get; set; }
}
{% endhighlight %}

### And now what?

There's more features to come, so watch out for new releases, which should arrive on a pretty regular basis in the weeks and months to come.

What I am looking for most, at the moment, is people in the software testing and the C# development communities willing to give the library a spin, tell me what they think and, most importantly, what issues they have found and what features they are still missing.

Please don't hold back and [create an issue on GitHub](https://github.com/basdijkstra/rest-assured-net/issues){:target="_blank"} if you see some room for improvement, I am sure there is lots that can be added and/or improved.