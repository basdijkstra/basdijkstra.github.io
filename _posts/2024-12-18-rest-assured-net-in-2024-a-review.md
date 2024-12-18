---
title: RestAssured .NET in 2024 - a review
layout: post
permalink: /rest-assured-net-in-2024-a-review/
categories:
  - RestAssured .NET
tags:
  - yearly review
  - features
  - bug fixes
---
As a (sort of) follow-up post to my [yearly review for 2024](/2024-a-year-in-review/), in this post, I would like to go over the changes, bug fixes and new features that have been introduced in [RestAssured .NET](https://github.com/basdijkstra/rest-assured-net){:target="_blank"} in 2024. This year, I released 7 new versions of the library, and while none of the versions included changes that were worthy of a blog post on its own, I thought it would be a good idea to wrap them all up in a single overview.

Basically, this blog post is an extended version of the library's [CHANGELOG](https://github.com/basdijkstra/rest-assured-net/blob/main/CHANGELOG.md){:target="_blank"}.

I'll go through the new versions chronologically, starting with the first release of 2024.

#### Version 4.2.2 - released April 23
RestAssured .NET 4.2.2 fixes [a bug that prevented JSON responses that are an array to be properly verified](https://github.com/basdijkstra/rest-assured-net/issues/119){:target="_blank"}. In other words, if the JSON response body looks like this:

{% highlight json %}
[
  {
    "id": 1,
    "text": "Do the dishes"
  },
  {
    "id": 2,
    "text": "Clean out the trash"
  },
  {
    "id": 3,
    "text": "Read the newspaper"
  }
]
{% endhighlight %}

I would expect this test to pass:

{% highlight csharp %}
[Test]
public void JsonArrayResponseBodyElementCanBeVerifiedUsingNHamcrestMatcher()
{
    Given()
        .When()
        .Get("http://localhost:9876/json-array-response-body")
        .Then()
        .StatusCode(200)
        .Body("$[1].text", NHamcrest.Is.EqualTo("Clean out the trash"));
}
{% endhighlight %}

but prior to this version, it threw a `Newtonsoft.Json.JsonReaderException`.

The solution? Adding a try-catch that first tries to parse the JSON response as a `JObject` (equal to existing behaviour), catch the `JsonReaderException` and try again, now parsing the JSON response into a `JArray`.

That made the newly added test pass without failing any other tests. Another demonstration of the added value of having a decent set of tests. RestAssured .NET is slowly growing and becoming more complex, and having a test suite I can run locally, and that always runs when I push code to GitHub is an invaluable safety net for me.

These tests run in a few seconds, yet they give me invaluable feedback on the effect of new features, bug fixes and code refactoring efforts.

I haven't heard back from the person submitting the original issue, but I assume that this fixed their issue.

#### Version 4.3.0 - released August 16
I love learning about how people use RestAssured .NET, because invariably they will use it in ways I haven't foreseen. I was unfamiliar with the concept of [server-sent events (SSE)](https://en.wikipedia.org/wiki/Server-sent_events){:target="_blank"} in APIs, for example, yet there are people looking to test these kinds of APIs using RestAssured .NET.

It turned out that [what this user was looking for](https://github.com/basdijkstra/rest-assured-net/discussions/125){:target="_blank"} was a way to set the `HttpCompletionOption` value on the `System.Net.Http.HttpClient` that is wrapped by RestAssured .NET. To enable this, I added a method to the DSL that looks like this:

{% highlight csharp %}
Given()
    .UseHttpCompletionOption(HttpCompletionOption.ResponseHeadersRead)
{% endhighlight %}

I also added the option to specify the `HttpCompletionOption` to be used in a `RequestSpecification` as well as in the global config. A straightforward fix that solved the problem for this specific user. The only thing I don't like here is that I don't know of a way to test this locally. Do you? I would love to hear it.

#### Version 4.3.1 - release August 22
Another user pointed out to me that [trying to verify that the value of a JSON response body element is an empty array also threw an exception](https://github.com/basdijkstra/rest-assured-net/issues/122){:target="_blank"}. So, if the JSON response body looks like this:

{% highlight json %}
{
  "success": true,
  "errors": []
}
{% endhighlight %}

this test should pass, but instead it threw a `Newtonsoft.Json.JsonSerializationException`:

{% highlight csharp %}
[Test]
public void JsonResponseBodyElementEmptyArrayValueCanBeVerifiedUsingNHamcrestMatcher()
{
    Given()
        .When()
        .Get("http://localhost:9876/json-empty-array-response-body")
        .Then()
        .StatusCode(200)
        .Body("$.errors", NHamcrest.Is.OfLength(0));
}
{% endhighlight %}

The fix? Adding some code that checks if the element returned when evaluating the JsonPath expression is a `JArray` or a `JObject` and using the right matching logic accordingly. I used my preferred procedure here:

* first, write a failing test that reproduces the issue
* then, make the test pass without breaking any other tests
* refactor the code, document and release

[Does this procedure sound familiar to you?](https://en.wikipedia.org/wiki/Acceptance_test-driven_development){:target="_blank"}

#### Version 4.4.0 - released October 21
As you can probably tell from the [semantic versioning](https://semver.org/){:target="_blank"}, this version introduced a new feature to RestAssured .NET: [the ability to use NTLM authentication when making an HTTP call](https://github.com/basdijkstra/rest-assured-net/issues/130){:target="_blank"}. To enable this, I added a new method to the DSL:

{% highlight csharp %}
Given()
    .NtlmAuth()  // This one uses default NTLM credentials for the current user
    .NtlmAuth("username", "password", "domain")  // This one uses custom NTLM credentials
{% endhighlight %}

As I had no idea how to write a proper test for this, even though I had tested it before releasing using Fiddler, I released a beta version first that the person submitting the issue could use to verify the solution. I'm happy to say that it worked for them and that the solution could be released properly.

Again, if someone can think of a way to add a proper test for NTLM authentication to the test suite, I would love to hear it. All that the [current tests](https://github.com/basdijkstra/rest-assured-net/blob/main/RestAssured.Net.Tests/NtlmAuthenticationTests.cs){:target="_blank"} do is run the code and see if no exception is thrown. Not a good test, but until I find a better way, it will have to do.

#### Version 4.5.0 - released November 19
This version introduced not one, but two changes. First, since .NET 9 was officially released earlier that week (or maybe the week before, I forgot), I needed to release a RestAssured .NET version that targets .NET 9, so I did. Just like with .NET 8, I didn't really have to change anything to the code other than adding `net9.0` to the `TargetFrameworks` and add .NET 9 to the build pipeline for the library to make sure that every change is tested on .NET 9, too. Happy to say it all 'just worked'.

The other change took more effort: a user reported that they [could not override the `ResponseLogLevel`](https://github.com/basdijkstra/rest-assured-net/issues/128){:target="_blank"} set in a `RequestSpecification` at the individual test level.

The reason? In the existing code, the response was logged directly after the HTTP call completed, so _before_ any calls to `Log()` for the response. When `Log()` is called on the response, it was then logged _again_. I have no idea how I completely overlooked this until now, but I did.

Rewriting the code to make this work took longer than I expected, but I managed in the end, through quite a bit of trial and error and lots of humand-centered testing (again, no idea how to write automated tests for this).

The logging functionality of RestAssured .NET is something I intend to rewrite in the future, for a couple of reasons:

* It's impossible to write automated tests for it (or at least I don't know how to do this)
* Ideally, I want the logging to be more configurable and extensible to give users more flexibility than they have at the moment

#### Version 4.5.1 - released November 20
As one does, I found an issue with the updated logging logic almost immediately after releasing 4.5.0 to the public: [masking of sensitive headers and cookies didn't work anymore](https://github.com/basdijkstra/rest-assured-net/issues/131){:target="_blank"} when specified as part of a `RequestSpecification`.

Lucky for me, this was a quick fix, but a bit embarrassing nonetheless. Had I had proper automated tests for the logging in place, I probably would have caught this before releasing 4.5.0.... Anyway, it's fixed now, as far as I can tell.

#### Version 4.6.0 - released December 9
The final RestAssured .NET release of 2024 added the capability to [strip the `; charset=<some_charset>` from the `Content-Type` header in a request](https://github.com/basdijkstra/rest-assured-net/issues/132){:target="_blank"}. It turns out, some APIs explicitly expect this header to _not_ contain the `charset` suffix, but the way I create a request, or rather, the way .NET creates a `StringContent` object, will add it by default.

This issue was a great example of one of the main reasons why I started this project: there is _so much_ I don't know yet about HTTP, APIs, C#/.NET and other technologies, and working on these issues and improving RestAssured .NET gives me an opportunity to learn them. I make a habit of writing what I learned down in the issue on GitHub, so I can review it later, and so I can point others to these links and thoughts, too.

So, if you're looking for a way to strip the `charset` identifier from the `Content-Type` header in the request, you can now do that by passing an optional second boolean argument to `Body()` (defaults to `false`):

{% highlight csharp %}
Given()
    .Body(your_body_goes_here, stripCharset: true)
{% endhighlight %}

That's it! As you can see, lots of small changes, bug fixes and new features have been added to RestAssured .NET this year. Oh, and before I forget: with every release, I also made sure to update the dependencies I use to create and test RestAssured .NET to their latest versions. I consider that good housekeeping, and it's all part of keeping a library up to date.

I am looking forward to seeing the library evolve and improve further in 2025.