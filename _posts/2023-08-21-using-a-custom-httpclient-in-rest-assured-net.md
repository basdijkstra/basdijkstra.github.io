---
title: Using a custom HttpClient in RestAssured.Net
layout: post
permalink: /using-a-custom-httpclient-in-rest-assured-net/
categories:
  - API testing
tags:
  - RestAssured.Net
  - ASP.NET Core
  - test automation
---
Last week, I had the pleasure of being a guest on [the Test Automation Experience show](https://www.youtube.com/@test-automation-experience){:target="_blank"} with [Nikolay Advolodkin](https://www.linkedin.com/in/nikolayadvolodkin){:target="_blank"}. In this episode, we talked about [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"}, I released version 4.1.0 during the podcast, and we discussed some features that have been added to the library in the last couple of releases.

If you want to watch the episode, [you can find it on YouTube here](https://www.youtube.com/watch?v=jQ6W9kisdnQ){:target="_blank"}.

In this short blog post, I want to highlight the most important new feature of RestAssured.Net version 4.1.0, which is the ability to inject a custom `System.Net.Http.HttpClient` ([docs](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=net-7.0){:target="_blank"}) into your tests.

#### Where is this useful?
By default, RestAssured.Net creates its own `HttpClient` to send the requests you create using the fluent syntax that the library provides, for example:

{% highlight csharp %}
[Test]
public void GetDataForUser1_CheckName_ShouldBeLeanneGraham()
{
Given()
.When()
.Get("https://jsonplaceholder.typicode.com/users/1")
.Then()
.StatusCode(HttpStatusCode.OK)
.Body("$.name", NHamcrest.Is.EqualTo("Leanne Graham"));
}
{% endhighlight %}

In many situations, the default `HttpClient` created by RestAssured.Net is perfectly capable of running the tests you want to perform.

In some cases, however, you either

* are working with a framework that provides and manages its own `HttpClient`, most notably [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing){:target="_blank"}, which can be used to write tests for [ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-7.0){:target="_blank"} APIs
* want to do some custom configuration on the `HttpClient` and/or the associated `HttpClientHandler` ([docs](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httpclienthandler?view=net-7.0){:target="_blank"})

In those cases, it would be useful to hand-roll an `HttpClient` and inject it into your RestAssured.Net tests.

And that's exactly what you can do starting from RestAssured.Net 4.1.0:

{% highlight csharp %}
[Test]
public void UsingACustomHttpClient()
{
var webAppFactory = new WebApplicationFactory<Program>();
var httpClient = webAppFactory.CreateDefaultClient();

    Given(httpClient)
    .When()
        .Get("http://localhost:5154/weatherforecast")
    .Then()
        .StatusCode(HttpStatusCode.OK);
}
{% endhighlight %}

This example contains a test for an ASP.NET Core [minimal API](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-7.0){:target="_blank"}, which is contained in the `Program` class. To be able to write efficient tests, where the `Microsoft.AspNetCore.Mvc.Testing` library handles managing configuring and starting and stopping the API in-memory, you'll need to use the `HttpClient` returned by the `CreateDefaultClient()` or the `CreateClient()` method.

As you can see, RestAssured now supports this by passing the `HttpClient` as an argument to the `Given()` method. If an `HttpClient` is supplied, RestAssured.Net will use that one, if none is supplied, it will generate its own and use that to send requests and receive responses.

I'm very happy that this feature is now finally available in RestAssured.Net, because I believe that it makes the library much more versatile, and it opens up a lot of new use cases.

As always, I'd love to hear what you think!