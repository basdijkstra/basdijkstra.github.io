---
title: Writing API tests in JavaScript with Pactum
layout: post
permalink: /writing-api-tests-in-javascript-with-pactum/
categories:
  - api testing
tags:
  - javascript
  - api
  - api-testing
---
One of the things I keep telling myself (and often, the people in my training sessions, too) is that JavaScript and I don't get along very well. It might be me, it might be JavaScript, but for some reason, I can't seem to get a good grasp of the language. Having been brought up in university on much more strictly typed languages (Java, most notably) probably didn't help.

There's no denying, though, that JavaScript is a popular language, and that there's quite the demand for automation skills in JavaScript. This is why I thought it'd be a good idea to spend a little more time exploring the language and the tools that are available these days.

As a lot of the technical blog posts I've written concern API testing of some sort (including API mocking and contract testing), yet until recently I wasn't really impressed with the API testing libraries available for JavaScript, I thought it'd be a good idea to make that my starting point.

I'm a big fan of API libraries like [REST Assured](https://rest-assured.io/){:target="_blank"} for Java and [requests](https://pypi.org/project/requests/){:target="_blank"} for Python, as they both make it really straightforward to write API tests.

I was not aware of a JavaScript library that was just as easy to use, until I recently saw someone posting an article about [Pactum](https://pactumjs.github.io/#/){:target="_blank"} on LinkedIn (I can't find the actual post right now, unfortunately), and thought it looked pretty good, so I thought it would be a good idea to take it for a test drive.

The fact that I committed to doing a talk and live demo on API testing in JavaScript for a group of software testing students definitely helped. Nothing like a public speaking commitment to get me to explore something new...

Looking at the docs, Pactum seems to be able to do quite a few things. Integration testing, API mocking, contract testing, ... It's all in there. In this blog post, I'll focus on the API testing (integration testing) capabilities of the library, but I might very well explore some of the other Pactum features in future blog posts and make a series out of it.

The examples you'll see in this blog post are written using [Jest](https://jestjs.io/){:target="_blank"}  as the testing framework.

Let's start with the 'Hello, world!' of API testing: performing a GET request to an endpoint of choice and checking the HTTP status code of the response. In Pactum, that can be done like this:

{% highlight javascript %}
describe('Retrieving data for user with ID 1', () => {

    test('should yield HTTP status code 200', async () => {

        await pactum.spec()
            .get('http://jsonplaceholder.typicode.com/users/1')
            .expectStatus(200)
    });
});
{% endhighlight %}

`pactum.spec()` exposes all methods offered by Pactum to construct a request. Since we don't need to specify anything in terms of headers, cookies or a request body, we can directly invoke an HTTP GET using the `get()` method, passing in the endpoint of our choice, and then specify our expectations around the response. In this case, our only expectation is that the HTTP status code equals 200, which we can verify using the `expectStatus()` method.

Running the test (using `npm run test` which in turns invokes Jest) shows that our test passes:

![pactum_passed_test](/images/blog/pactum_passed_test.png "Running a test in Pactum and seeing that it passes")

As a next step, let's see if we can check the value of a response header, in this case the Content-Type header:

{% highlight javascript %}
test('should yield Content-Type header containing value "application/json"', async () => {

    await pactum.spec()
        .get('http://jsonplaceholder.typicode.com/users/1')
        .expectHeaderContains('content-type', 'application/json')
});
{% endhighlight %}

The `expectHeaderContains()` method does exactly what it says on the tin: it looks for a response header and checks that its value contains a predefined expected value, in this case `application/json`. One thing to beware of is that for some reason you need to specify the header name in lowercase characters. I initially used `Content-Type`, but that made the test fail because it couldn't find a header by that name.

Oh, and if you want a method that performs an exact match, use `expectHeader()` instead.

Next, let's look at the response body. Pactum supports JSON response bodies really well, for other response bodies (plain text, XML, ...) support seems to be limited to string-based comparison, which means you'll have to do a little more work yourself. Our API under test returns data in JSON format, so that's not a problem here.

Say we want to check that the top level element `name` has a value equal to `Leanne Graham` in our JSON response. Using the `expectJsonMatch()` method in Pactum makes this a straightforward thing to do:

{% highlight javascript %}
test('should yield "name" JSON body element with value "Leanne Graham"', async () => {

    await pactum.spec()
        .get('http://jsonplaceholder.typicode.com/users/1')
        .expectJsonMatch('name', 'Leanne Graham')
});
{% endhighlight %}

The first argument to `expectJsonMatch()` is actually a [json-query](https://www.npmjs.com/package/json-query){:target="_blank"} expression, so it can be used to retrieve nested objects, too, for example:

{% highlight javascript %}
test('should yield "Gwenborough" as the city within the address', async () => {

    await pactum.spec()
        .get('http://jsonplaceholder.typicode.com/users/1')
        .expectJsonMatch('address.city', 'Gwenborough')
});
{% endhighlight %}

So, what about POSTing some data to an endpoint instead of retrieving and checking data from an endpoint? It turns out that, too, is really straightforward using Pactum:

{% highlight javascript %}
describe('Posting a new post item', () => {

    test('should yield HTTP status code 201', async () => {

        let new_post = {
            "title": "My awesome new post title",
            "body": "My awesome new post body",
            "userId": 1
        }

        await pactum.spec()
            .post('http://jsonplaceholder.typicode.com/posts')
            .withJson(new_post)
            .expectStatus(201)
    });
});
{% endhighlight %}

Creating a JSON payload is as easy as specifying (creating) it and using the `withJson()` method to add it to your request.

As a final example, I often look at how easy it is to create data-driven tests with an API library. Since APIs often expose business logic, and you'll often need more than one combination of input and corresponding expected output values to properly verify that logic, data-driven tests are a fairly common thing when writing tests for APIs.

Now, Jest does a lot of the heavy lifting for us (like JUnit would in Java, or pytest would in Python) by providing a mechanism for data-driven tests using `test.each()`:

{% highlight javascript %}
describe('Retrieving user data for users', () => {

    test.each(
        [[1,'Leanne Graham'], [2,'Ervin Howell'], [3,'Clementine Bauch']]
    )('User with ID %i has name %s', async (userId, expectedName) => {

        await pactum.spec()
            .get('http://jsonplaceholder.typicode.com/users/{user}')
            .withPathParams('user', userId)
            .expectJsonMatch('name', expectedName)
    });

});
{% endhighlight %}

All we need to add when writing our Pactum-based test is specify a path parameter using the `withPathParams()` method and using that to populate the `{user}` placeholder in the endpoint. The mechanism is really similar to what I'm used to do in Java, C# or Python, which definitely helps me appreciate Pactum (and Jest, and even JavaScript in general) a lot more.

Running this test yields the following output:

![pactum_data_driven_test](/images/blog/pactum_data_driven_test.png "Running a test in Pactum and seeing that it passes")

What you've seen in this post are just some of the examples of what you can do with Pactum. Judging from [the docs](https://pactumjs.github.io/#/welcome){:target="_blank"}, there's a lot more you can do with the library, and I'm looking forward to exploring the Pactum features, especially the mocking and contract testing capabilities, in the future. Never thought I'd say that about a JavaScript library...

All code and examples shown in this blog post can be found [here](https://github.com/basdijkstra/api-testing-js-pactum){:target="_blank"}.