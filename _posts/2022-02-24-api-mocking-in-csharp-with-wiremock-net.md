---
title: API mocking in C# with WireMock.Net
layout: post
permalink: /api-mocking-in-csharp-with-wiremock-net/
categories:
  - api mocking
tags:
  - c#
  - api
  - api-mocking
---
Those of you who have been reading my blog posts for a while probably know I am a big fan of [WireMock](https://wiremock.org/){:target="_blank"}, the open source API mocking / service virtualization tool written in Java. I've even created and published a [free and open source workshop](https://github.com/basdijkstra/wiremock-workshop){:target="_blank"} around the tool.

I only much more recently found out, while preparing an API testing course in C# for a client, that WireMock has been ported to C# as well. In this blog post, I'd like to take a closer look at [WireMock.Net](https://github.com/WireMock-Net/WireMock.Net){:target="_blank"}, as the library is (rather predictably) called. Before we do that, all credits for writing and maintaining the library go to [Stef Heyenrath](https://nl.linkedin.com/in/stef-heyenrath-33775a3){:target="_blank"}.

I'm not going to dive too deeply into the use cases for WireMock.Net, as it has the exact same purpose as the 'original' WireMock: allowing you to mock internal or external APIs in order to write more efficient tests and test earlier, more and more often. This is also not an exhaustive overview of all WireMock.Net features, but rather a collection of examples of what I think are its most important or powerful capabilities.

### Defining and testing a first mock API response
After adding WireMock.Net to your project using the [NuGet package](https://www.nuget.org/packages/WireMock.Net/){:target="_blank"}, using WireMock.Net to mock an API call in a test can be done as follows:

{% highlight csharp %}
private WireMockServer server;

[SetUp]
public void StartServer()
{
// This starts a new mock server instance listening at port 9876
server = WireMockServer.Start(9876);
}

private void CreateHelloWorldStub()
{
    // This defines a mock API response that responds to an incoming HTTP GET
    // to the `/hello-world` endpoint with a response with HTTP status code 200,
    // a Content-Type header with value `text/plain` and a response body
    // containing the text `Hello, world!`
    server.Given(
        Request.Create().WithPath("/hello-world").UsingGet()
    )
    .RespondWith(
        Response.Create()
            .WithStatusCode(200)
            .WithHeader("Content-Type", "text/plain")
            .WithBody("Hello, world!")
    );
}

[TearDown]
public void StopServer()
{
    // This stops the mock server to clean up after ourselves
    server.Stop();
}
{% endhighlight %}

We could then write a test for this mock API using [RestSharp](https://restsharp.dev/){:target="_blank"}, for example:

{% highlight csharp %}
private RestClient client;

[Test]
public async Task TestHelloWorldStub()
{
    // This creates the mock API response we defined earlier
    CreateHelloWorldStub();

	// Define the HTTP request to be sent
	client = new RestClient("http://localhost:9876");

	RestRequest request = new RestRequest("/hello-world", Method.Get);

	// Send the HTTP request to the mock server
	RestResponse response = await client.ExecuteAsync(request);

	// Check that the response returned by the mock server contains the expected properties
	Assert.That(response.StatusCode, Is.EqualTo(HttpStatusCode.OK));
	Assert.That(response.ContentType, Is.EqualTo("text/plain"));
	Assert.That(response.Content, Is.EqualTo("Hello, world!"));
}
{% endhighlight %}

This test will pass, telling us that our mock API works as expected.

Now that we've established how WireMock.Net works, let's have a look at some key features I look for in all API mocking tools and libraries, and see how WireMock.Net implements those.

### Request matching strategies
The first thing I usually explore in API mocking tools is their capabilities regarding request matching, or the identification of specific characteristics of a request in order to determine the appropriate response.

As mentioned in the [documentation on request matching](https://github.com/WireMock-Net/WireMock.Net/wiki/Request-Matching){:target="_blank"}, WireMock.Net can match requests based on path or URL, HTTP method, query parameters, headers, cookies and request bodies.

The example above introduced two of these request matching strategies already, being the URL and the HTTP method. The mock defined there only responds to HTTP GET calls to the `/hello-world` endpoint.

Let's look at a couple of other examples, starting with matching requests that do (or do not) contain a specific header with a specific value. This is very useful, for example, when dealing with API authentication mechanisms.

{% highlight csharp %}
private void CreateStubHeaderMatching()
{
    server.Given(
        Request.Create().WithPath("/header-matching").UsingGet()
            // this makes the mock only respond to requests that contain
            // a 'Content-Type' header with the exact value 'application/json'
            .WithHeader("Content-Type", new ExactMatcher("application/json"))
            // this makes the mock only respond to requests that do not contain
            // the 'ShouldNotBeThere' header
            .WithHeader("ShouldNotBeThere", ".*", matchBehaviour: MatchBehaviour.RejectOnMatch)
    )
    .RespondWith(
        Response.Create()
            .WithStatusCode(200)
            .WithBody("Header matching successful")
    );
}
{% endhighlight %}

Other than the `ExactMatcher`, which checks for an exact value match, there are a number of [other useful matchers](https://github.com/WireMock-Net/WireMock.Net/wiki/Request-Matching#matchers){:target="_blank"} available, including various JSON matchers and a regex matcher.

We can also match on JSON request body elements, like in this example:

{% highlight csharp %}
private void CreateStubRequestBodyMatching()
{
    server.Given(
        Request.Create().WithPath("/request-body-matching").UsingPost()
            // this makes the mock only respond to requests with a JSON request body
            // that produces a match for the specified JSON path expression
            .WithBody(new JsonPathMatcher("$.cars[?(@.make == 'Alfa Romeo')]"))
    )
    .RespondWith(
        Response.Create()
            .WithStatusCode(201)
    );
}
{% endhighlight %}

This will return a match for this request body, for example:

`{ "cars": [ { "make": "Alfa Romeo" }, { "make": "Lancia" } ] }`

### Simulating faults and delays
One thing where using mocks shines over having to deal with real dependencies is testing the resilience of your system against faulty or unexpected behaviour of such a dependency. Where delays and errors are often really hard, if not impossible to trigger in a real dependency, it is just another bit of behaviour to model in a mock.

Next to returning HTTP 4xx or 5xx response status codes, which is a matter of passing the right value to the `WithStatusCode()` method when building your response, WireMock.Net also supports other types of unexpected behaviour. Here's an example that returns a valid response, but with a predefined delay:

{% highlight csharp %}
private void CreateStubReturningDelayedResponse()
{
    server.Given(
        Request.Create().WithPath("/delay").UsingGet()
    )
    .RespondWith(
        Response.Create()
           .WithStatusCode(200)
           // this returns the response after a 2000ms delay
           .WithDelay(TimeSpan.FromMilliseconds(2000))
    );
}
{% endhighlight %}

Using `WithDelay()` allows you to model performance behaviour and check that dependency timeouts and delayed responses are handled correctly in your application under test.

WireMock.Net also provides some capabilities for returning malformed responses:

{% highlight csharp %}
private void CreateStubReturningFault()
{
    server.Given(
        Request.Create().WithPath("/fault").UsingGet()
    )
    .RespondWith(
        Response.Create()
            // returns a response with HTTP status code 200
            // and garbage in the response body
            .WithFault(FaultType.MALFORMED_RESPONSE_CHUNK)
    );
}
{% endhighlight %}

### Creating stateful stubs
Another useful mocking feature is the ability to simulate statefulness ('memory'). Sometimes, you want to model stateful behaviour, i.e., behaviour where the order in which incoming requests arrive is significant.

WireMock.Net supports this through building [finite state machines](https://en.wikipedia.org/wiki/Finite-state_machine){:target="_blank"} (FSMs), i.e., collections of states and state transitions:

{% highlight csharp %}
private void CreateStatefulStub()
{
    server.Given(
        Request.Create().WithPath("/todo/items").UsingGet()
    )
    // In this scenario, when the current state is 'TodoList State Started',
    // a call to an HTTP GET will only return 'Buy milk'
    .InScenario("To do list")
    .WillSetStateTo("TodoList State Started")
    .RespondWith(
        Response.Create()
            .WithBody("Buy milk")
    );

	server.Given(
		Request.Create().WithPath("/todo/items").UsingPost()
	)
	// In this scenario, when the current state is 'TodoList State Started',
	// a call to an HTTP POST will trigger a state transition to new state
	// 'Cancel newspaper item added'
	.InScenario("To do list")
	.WhenStateIs("TodoList State Started")
	.WillSetStateTo("Cancel newspaper item added")
	.RespondWith(
		Response.Create()
            .WithStatusCode(201)
	);

	server.Given(
		Request.Create().WithPath("/todo/items").UsingGet()
	)
	// In this scenario, when the current state is 'Cancel newspaper item added',
	// a call to an HTTP GET will return 'Buy milk;Cancel newspaper subscription'
	.InScenario("To do list")
	.WhenStateIs("Cancel newspaper item added")
	.RespondWith(
		Response.Create()
            .WithBody("Buy milk;Cancel newspaper subscription")
	);
}
{% endhighlight %}

An HTTP GET request to `/todo/items` arriving _before_ a POST to the same endpoint will return a to-do list with a single item 'Buy milk' on it, whereas that same GET call, when received _after_ a POST, will return a to-do list containing both 'Buy milk' and 'Cancel newspaper subscription'.

### Response templating
The final feature I typically investigate when looking at an API mocking tool is its ability to reuse values from an incoming request in the corresponding response.

This is useful when dealing with values that are necessary for successful processing of a response on the consumer side, but that are impossible to predict beforehand. Think transaction or session IDs, cookies, and other such values.

Like its Java counterpart, WireMock.Net supports reusing request values through [response templating](https://github.com/WireMock-Net/WireMock.Net/wiki/Response-Templating){:target="_blank"}. Here's an example that inspects the request, extracts the HTTP method used and echoes that in the response body:

{% highlight csharp %}{% raw %}
private void CreateStubEchoHttpMethod()
{
    server.Given(
        Request.Create().WithPath("/echo-http-method").UsingAnyMethod()
    )
    .RespondWith(
        Response.Create()
            .WithStatusCode(200)
            // The {{request.method}} handlebar extracts the HTTP method from the request
            .WithBody("HTTP method used was {{request.method}}")
            // This enables response templating for this specific mock response
            .WithTransformer()
    );
}
{% endraw %}{% endhighlight %}

The `WithTransformer()` method has to be added to the response definition, otherwise the response body will include the literal value `{{request.method}}` as no response templating will be applied.

Unfortunately, there does not seem to be a way to apply this on a global level (as can be done in the WireMock in Java, so you will need to add this to each mock response definition.

Here's another example that uses response templating, this time to extract a value from the request JSON body to reuse it in the plain text response body:

{% highlight csharp %}{% raw %}
private void CreateStubEchoJsonRequestElement()
{
    server.Given(
        Request.Create().WithPath("/echo-json-request-element").UsingPost()
    )
    .RespondWith(
        Response.Create()
            .WithStatusCode(200)
            // This extracts the book.title element from the JSON request body
            // (using a JsonPath expression) and repeats it in the response body
            .WithBody("The specified book title is {{JsonPath.SelectToken request.body \"$.book.title\"}}")
            .WithTransformer()
    );
}
{% endraw %}{% endhighlight %}

As you can see, WireMock.Net allows you to extract element values from a (JSON) request body using [JsonPath expressions](https://goessner.net/articles/JsonPath/){:target="_blank"} and reuse them, for example to repeat them in response bodies.

That wraps up this first look at WireMock.Net. Again, it is by no means meant to be a complete overview of everything that WireMock.Net can do for you, but rather a look at some features I find particularly interesting and valuable.

I've been pleasantly surprised by its ease of use, although I must admit I probably had an unfair advantage having a couple of years of experience with WireMock in Java under my belt. I'm looking forward to further explore WireMock.Net in the future.

If you'd like to know more about WireMock.Net, I recommend you have a look at [the docs](https://github.com/WireMock-Net/WireMock.Net/wiki){:target="_blank"}. I've put all the examples I used in this blog post [on GitHub](https://github.com/basdijkstra/wiremock-net-examples){:target="_blank"}, together with NUnit + RestSharp tests to demonstrate that the mock API responses work as expected.