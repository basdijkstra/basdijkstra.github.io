---
title: API mocking in JavaScript with Pactum
layout: post
permalink: /api-mocking-in-javascript-with-pactum/
categories:
  - api mocking
tags:
  - javascript
  - api
  - api-mocking
---
A while ago, I wrote a [blog post about Pactum](/writing-api-tests-in-javascript-with-pactum/), a JavaScript library for API testing, mocking and contract testing. In that blog post, I focused on the API testing capabilities that Pactum provided. In this post, I'd like to continue my exploration of Pactum by looking a little more closely to its API mocking features.

I do have prior experience with API mocking libraries, most notably [WireMock](/getting-started-with-wiremock/) and [WireMock.Net](/api-mocking-in-csharp-with-wiremock-net/), and in this blog post, I'll take a look at how Pactum compares to these two by looking at some essential features that I look for in any API mocking tool or library.

### Getting started: setting up and testing our first mock
Let's start with a simple 'Hello, world!' example. Like WireMock and WireMock.Net, Pactum offers a mock server to which you can add mock responses. Like myself, users of the aforementioned tools shouldn't have a lot of trouble getting started with Pactum.

Starting the Pactum mock server is simple:

{% highlight javascript %}
const { mock, settings } = require('pactum');

beforeEach(async () => {

    settings.setLogLevel('ERROR');

    await mock.start(9876);
});
{% endhighlight %}

I'm using the [Jest](https://jestjs.io/){:target="_blank"} `beforeEach` construct here to start the mock server prior to each test. I then set the log level to `ERROR` to get rid of some startup and shutdown logging that Pactum writes to the console by default, as I don't need that. Finally, I start the mock server on port 9876. That's it.

Shutting down the mock server after each test is just as straightforward:

{% highlight javascript %}
afterEach(async () => {

    await mock.stop()
});
{% endhighlight %}

If you want to start / stop the server only once, before the test run, you can replace `beforeEach` and `afterEach` with `beforeAll` and `afterAll`, respectively. I'll leave that up to you. Starting and stopping the server is really, really quick, so I didn't notice any performance degradation by doing it like this.

Now that we can start and stop the server, let's add a first mock response to it:

{% highlight javascript %}
function addHelloWorldResponse() {

    mock.addInteraction({
        request: {
            method: 'GET',
            path: '/api/hello-world'
        },
        response: {
            status: 200,
            body: 'Hello, world!'
        }
    });
}
{% endhighlight %}

Mock responses are added to the Pactum mock server by means of interactions. An interaction contains information about the request to respond to, used for request matching (more on that soon), as well as the mock response that should be returned. In this case, we want to respond to an HTTP GET to `/api/hello-world` with a response with HTTP status code 200 and a plaintext response body `Hello, world!`.

To test whether this works, we can [write a test using Pactum](/writing-api-tests-in-javascript-with-pactum/) that invokes the mock endpoint on our `localhost` machine at port 9876:

{% highlight javascript %}
const pactum = require('pactum');

describe('Demonstrating that Pactum API mocking can', () => {

    test('return a basic REST response', async () => {

        addHelloWorldResponse();

        await pactum.spec()
            .get('http://localhost:9876/api/hello-world')
            .expectStatus(200)
            .expectBody('Hello, world!')
    });
});
{% endhighlight %}

Running this test results in the following output, showing us that our mock behaves as expected:

![pactum_mock_passing_test](/images/blog/pactum_mock_passing_test.png "Running a test and seeing that the mock behaves as expected")

### Request matching
In the previous example, request matching (i.e., looking at specific characteristics of the incoming request to determine the appropriate response) was done by looking at the HTTP verb (GET) and the endpoint (`/api/hello-world`). Pactum offers a couple of other request matching strategies, too, including on request headers and their values (useful for authentication), query parameters and their values, and the contents of the request body.

Here's an example of how to add responses for requests with a specific query parameter value:

{% highlight javascript %}
function addQueryParameterRequestMatchingResponses() {

    mock.addInteraction({
        request: {
            method: 'GET',
            path: '/api/zip',
            queryParams: {
                zipcode: 90210
            }
        },
        response: {
            status: 200,
            body: {
                zipcode: 90210,
                city: 'Beverly Hills'
            }
        }
    });

    mock.addInteraction({
        request: {
            method: 'GET',
            path: '/api/zip',
            queryParams: {
                zipcode: 12345
            }
        },
        response: {
            status: 200,
            body: {
                zipcode: 12345,
                city: 'Schenectady'
            }
        }
    });
}
{% endhighlight %}

This will instruct the Pactum mock server to respond to:

* an HTTP GET to `/api/zip?zipcode=90210` with a response body `{zipcode: 90210, city: 'Beverly Hills'}`
* an HTTP GET to `/api/zip?zipcode=12345` with a response body `{zipcode: 12345, city: 'Schenectady'}`
* all other requests (including those to `/api/zip` with different values for the `zipcode` query parameter) with an HTTP 404 (the default response for an unmatched request)

The [GitHub repository](https://github.com/basdijkstra/api-testing-js-pactum){:target="_blank"} contains tests to demonstrate that the mock defined above behaves as expected.

### Performance behaviour simulation
Another useful feature of any API mocking library is the capability to define performance behaviour, or the possibility to determine how long the mock server should wait before responding to an incoming request. This mock definition, for example, returns a response after waiting for a fixed delay of 1000 milliseconds:

{% highlight javascript %}
function addDelayedResponse() {

    mock.addInteraction({
        request: {
            method: 'GET',
            path: '/api/delay'
        },
        response: {
            status: 200,
            fixedDelay: 1000
        }
    })
}
{% endhighlight %}

For even more realistic performance behaviour, Pactum also enables you to randomize the delay and provide minimum and maximum delay values.

The following test, invoking the mock responding with a delay, fails:

{% highlight javascript %}
test('return a REST response with a delay', async () => {

    addDelayedResponse();

    await pactum.spec()
        .get('http://localhost:9876/api/delay')
        .expectStatus(200)
        .expectResponseTime(1000)
});
{% endhighlight %}

Pactum only allows you to specify an upper limit for the expected response time. Because the actual response time is over 1000 milliseconds (there's the delay we added, plus some processing time), the test fails, demonstrating that the delay was applied successfully. If that weren't the case, the test would have passed, because sending the request and processing the response typically only takes a couple of milliseconds.

### Reusing values from the request
Often, when mocking API response, you want to reuse values from the request (unique IDs, cookies, other dynamic values). With Pactum, you can do that, too:

{% highlight javascript %}
const { like } = require('pactum-matchers');

function addReusePathParameterValueResponse() {

    mock.addInteraction({
        request: {
            method: 'GET',
            path: '/api/user/{id}',
            pathParams: {
                id: like('random-id')
            }
        },
        stores: {
            userId: 'req.pathParams.id'
        },
        response: {
            status: 200,
            body: {
                message: `Returning data for user $S{userId}`
            }
        }
    });
}
{% endhighlight %}

This mock definition identifies a specific path segment as a path parameter `id`, referring to a user ID here, and stores it for reuse under the `userId` name. It can then be reused when constructing the response, which we're doing here by using it in a template string, referring to the previously stored value using `$S{userId}`. Please note the `S` in there, which I assume refers to something like the 'store' where values are stored by Pactum.

This data-driven test (again, see [the previous blog post](/writing-api-tests-in-javascript-with-pactum/)) shows that the Pactum mock server successfully extracts the path parameter value from the request, and reuses it in the response body:

{% highlight javascript %}
test.each(
[[1], [2], [3]]
)('use response templating to return the expected message for user %i', async (userId) => {

    addReusePathParameterValueResponse();

    await pactum.spec()
        .get('http://localhost:9876/api/user/{user}')
        .withPathParams('user', userId)
        .expectStatus(200)
        .expectJsonMatch('message', `Returning data for user ${userId}`)
});
{% endhighlight %}

In the same way, Pactum can extract query parameter values, header values, as well as request body values to be reused in the response.

Wrapping this up, I found the API mocking capabilities offered by Pactum pretty straightforward to use, although it probably helps that I've got some prior experience with WireMock under my belt.

One thing I didn't explore in this blog post is the capability to simulate statefulness, i.e., modeling 'state' or 'memory' in your API mock. Where WireMock does this [using finite state models](https://wiremock.org/docs/stateful-behaviour/){:target="_blank"}, with Pactum you can probably do something similar using the `onCall` construct, as demonstrated [here](https://pactumjs.github.io/mock-server?id=behavior-on-consecutive-calls){:target="_blank"}.

It's not the same as the FSM approach in WireMock (and WireMock.Net), but for simple scenarios, you should be able to get similar results.

All code shown in this blog post can be found [on GitHub](https://github.com/basdijkstra/api-testing-js-pactum){:target="_blank"}. I'm looking forward to exploring the contract testing capabilities of Pactum in a future blog post.