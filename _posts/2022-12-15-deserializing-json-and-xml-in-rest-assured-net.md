---
title: (De-)serializing JSON and XML in RestAssured.Net
layout: post
permalink: /deserializing-json-and-xml-in-rest-assured-net/
categories:
  - RestAssured.Net
tags:
  - csharp
  - api-testing
  - development
---
In a series of (short) blog posts, I would like to share with you some examples of how to use [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} for writing tests for RESTful and GraphQL APIs.

* [Getting started and basic examples](/getting-started-with-rest-assured-net/)
* [Parameterized tests](/parameterizing-tests-in-rest-assured-net/) 
* (De-)serialization (**this blog post**)
* Authentication and reuse
* Testing GraphQL APIs

All examples that you see in this blog post series [can be found on GitHub](https://github.com/basdijkstra/rest-assured-net-examples){:target="_blank"}.

### Serialization
Serialization is the process of creating a JSON or XML representation of a C# object. RestAssured.Net supports both JSON and XML, and in this blog post, we'll look at examples for both.

First, consider this C# object, representing a blog post:

{% highlight csharp%}
public class Post
{
    [JsonProperty("userId")]
    public int UserId { get; set; }

    [JsonProperty("title")]
    public string Title { get; set; } = string.Empty;

    [JsonProperty("body")]
    public string Body { get; set; } = string.Empty;

    public Post()
    {
    }
}
{% endhighlight %}

Now, if we want to create a JSON or an XML representation of an instance of this class, there are several ways to do that. We could, for example, build the JSON or XML 'by hand', however, that is error-prone and often clunky.

Therefore, we can also leverage the power of libraries like [Json.Net](https://www.newtonsoft.com/json){:target="_blank"} and [System.Xml](https://learn.microsoft.com/en-us/dotnet/api/system.xml){:target="_blank"}, respectively, to take care of the heavy lifting for us. And that's exactly what RestAssured.Net does for you, out of the box.

If we want to send a JSON representation of a `Post` object, all we need to do is pass an instance to the `Body()` method:

{% highlight csharp %}
[Test]
public void PostNewPostUsingPoco_CheckStatusCode_ShouldBe201()
{
    Post myNewPost = new Post()
    {
        UserId = 1,
        Title = "My new post title",
        Body = "This is the body of my new post"
    };

    Given()
    .Body(myNewPost)
    .When()
    .Post("http://jsonplaceholder.typicode.com/posts")
    .Then()
    .StatusCode(201);
}
{% endhighlight %}

When we run this test and log the request to the standard output, we see that the `Post` instance is serialized into this JSON payload:

{% highlight json %}
{
    "userId": 1,
    "title": "My new post title",
    "body": "This is the body of my new post"
}
{% endhighlight %}

Success! It also works with anonymous objects, by the way:

{% highlight csharp %}
[Test]
public void PostNewPostUsingAnonymousObject_CheckStatusCode_ShouldBe201()
{
    var myNewPost = new
    {
        userId = 1,
        title = "My new post title",
        body = "This is the body of my new post"
    };

    Given()
    .Body(myNewPost)
    .When()
    .Post("http://jsonplaceholder.typicode.com/posts")
    .Then()
    .StatusCode(201);
}
{% endhighlight %}

If we want to serialize our `Post` object to XML instead of JSON, we need to set the request `Content-Type` header to `application/xml`:

{% highlight csharp %}
[Test]
public void PostNewPostAsXmlUsingAnonymousObject_CheckStatusCode_ShouldBe201()
{
    Given()
    .ContentType("application/xml")
    .Body(myNewPost)
    .When()
    .Post("http://jsonplaceholder.typicode.com/posts")
    .Then()
    .StatusCode(201);
}
{% endhighlight %}

This will result in the following XML payload to be POSTed to the endpoint:

{% highlight xml%}
<Post xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
    <UserId>1</UserId>
    <Title>My new post title</Title>
    <Body>This is the body of my new post</Body>
</Post>
{% endhighlight %}

### Deserialization
We can also transform JSON or XML response payloads back into strongly typed C# objects, a process known as deserialization. In RestAssured.Net, this is done using the `As()` method:

{% highlight csharp %}
[Test]
public void GetPost_CheckTitle_ShouldBeExpectedValue()
{
    Post myPost =

    (Post)Given()
    .When()
    .Get("http://jsonplaceholder.typicode.com/posts/1")
    .As(typeof(Post));

    Assert.That(myPost.Title, Is.EqualTo("sunt aut facere repellat provident occaecati excepturi optio reprehenderit"));
}
{% endhighlight %}

This, too, works both for JSON and XML. RestAssured.Net inspects the `Content-Type` header value of the response and tries to deserialize the response according to its value, defaulting to JSON if no `Content-Type` header value can be found.

That's it for (de-)serialization and working with JSON and XML request and response payloads in RestAssured.Net. In the next blog post in this series, we'll look at extracting and reusing request properties and response values, as well as different API authentication mechanisms supported by RestAssured.Net.