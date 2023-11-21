---
title: Sending multipart requests in RestAssured.Net
layout: post
permalink: /sending-multipart-requests-in-rest-assured-net/
categories:
  - API testing
tags:
  - RestAssured.Net
  - multipart requests
  - test automation
---
This week sees the release of [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} version 4.2.0. Next to support for .NET 8, which has been released earlier this month, one of the most significant changes has been made in the support for creating and sending multipart requests. In this blog post, I want to share some more details on these improvements.

#### What are multipart requests?
Multipart requests allow you to send multiple sets of data in a single request body, separated by boundaries. They are typically used for file uploads, as well as for sending multiple types of data in a single request, such as a file combined with some metadata in JSON format.

Now, in earlier versions of RestAssured.Net, you couldn't actually send multiple sets of data in a request, even though you _could_ send one or more files using a multipart request. I was made aware of this omission through [an issue submitted on the RestAssured.Net repository](https://github.com/basdijkstra/rest-assured-net/issues/110){:target="_blank"}, and that triggered me to dive deeper into how multipart requests are created, and how the .NET `HttpRequestMessage` supports them.

That research led to much improved support for multipart requests in the latest version of RestAssured.Net. Let's have a look at some examples.

#### Example 1: uploading a file, the old way
Before version 4.2.0, RestAssured.Net supported uploading one or more files, but nothing else, as a multipart request body in the following way:

{% highlight csharp %}
Given()
    .MultiPart(new FileInfo("to_do.txt"))
.When()
    .Post("https://your.endpoint.com/accepting-multipart")
.Then()
    .StatusCode(201);
{% endhighlight %}

When the contents of the file are equal to 'Watch Office Space', this yields the following request:

{% highlight console %}
POST https://your.endpoint.com/accepting-multipart HTTP/1.1
Host: your.endpoint.com
Content-Type: multipart/form-data; boundary="c77ca8ba-f32a-4639-9496-875a36162f5a"
Content-Length: 231

--c77ca8ba-f32a-4639-9496-875a36162f5a
Content-Type: text/plain
Content-Disposition: form-data; name=file; filename=to_do.txt; filename*=utf-8''to_do.txt

Watch Office Space

--c77ca8ba-f32a-4639-9496-875a36162f5a--
{% endhighlight %}

Overloads are available for overriding the default control name (`file`) and the content type (automatically determined, `text/plain` here).

If you want to upload more than one file in a single request, you can simply chain multiple calls to `MultiPart()` together:

{% highlight csharp %}
Given()
    .MultiPart(new FileInfo("to_do.txt"))
    .MultiPart(new FileInfo("another_file.txt"))
.When()
    .Post("https://your.endpoint.com/accepting-multipart")
.Then()
     .StatusCode(201);
{% endhighlight %}

#### Example 2: uploading files with additional data
RestAssured.Net version 4.2.0 introduces a new way to upload multipart requests by means of a new method `MultiPart(string name, HttpContent content)`. This enables you to upload _any_ data in multipart form, as long as it is of type `HttpContent` ([documentation](https://learn.microsoft.com/en-us/dotnet/api/system.net.http.httpcontent){:target="_blank"}, or, in practice, of a type that derives from `HttpContent`.

For example, the same file can now also be uploaded alongside other fields using

{% highlight csharp %}
Given()
    .MultiPart(new FileInfo("Addresses.csv"))
    .MultiPart("projectId", new StringContent("PROJECT-1234"))
    .MultiPart("projectName", new StringContent("MyProject"))
.When()
    .Post("https://your.endpoint.com/accepting-multipart")
.Then()
    .StatusCode(201);
{% endhighlight %}

yielding

{% highlight console %}
POST https://your.endpoint.com/accepting-multipart HTTP/1.1
Host: your.endpoint.com
Content-Type: multipart/form-data; boundary="d0da5b48-705f-4d2e-bd02-96a4aa355e2a"
Content-Length: 568

--d0da5b48-705f-4d2e-bd02-96a4aa355e2a
Content-Type: text/csv
Content-Disposition: form-data; name=file; filename=Addresses.csv; filename*=utf-8''Addresses.csv

Street;Number;ZipCode;City
Kertzmann Circles;4273;20191;Kiaraview
Alan Road;4389;22333;New Hiltonville
Mayer Cliff;4275;66959;Hayleychester
Runolfsdottir Views;4774;23487;North Ilaport

--d0da5b48-705f-4d2e-bd02-96a4aa355e2a
Content-Type: text/plain; charset=utf-8
Content-Disposition: form-data; name=projectId

PROJECT-1234
--d0da5b48-705f-4d2e-bd02-96a4aa355e2a
Content-Type: text/plain; charset=utf-8
Content-Disposition: form-data; name=projectName

MyProject
--d0da5b48-705f-4d2e-bd02-96a4aa355e2a--
{% endhighlight %}

If you don't want to make multiple calls to `MultiPart()`, you can also pass in an object of type `Dictionary<string, HttpContent>`:

{% highlight csharp %}
Dictionary<string, HttpContent> additionalMultipartPayload = new Dictionary<string, HttpContent>()
{
    { "projectId", new StringContent("PROJECT-1234") },
    { "projectName", new StringContent("MyProject") },
};

Given()
    .MultiPart(new FileInfo("Addresses.csv"))
    .MultiPart(additionalMultipartPayload)
.When()
    .Post($"{MOCK_SERVER_BASE_URL}/csv-multipart-form-data-additional-fields")
.Then()
    .StatusCode(201);
{% endhighlight %}

More examples of multipart form request creation can be found in [the RestAssured.Net acceptance tests](https://github.com/basdijkstra/rest-assured-net/blob/main/RestAssured.Net.Tests/MultiPartFormDataTests.cs){:target="_blank"}.

RestAssured.Net can be found on [NuGet](https://www.nuget.org/packages/RestAssured.Net){:target="_blank"}.