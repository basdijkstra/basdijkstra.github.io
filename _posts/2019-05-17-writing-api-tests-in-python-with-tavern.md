---
title: Writing API tests in Python with Tavern
layout: post
permalink: /writing-api-tests-in-python-with-tavern/
categories:
  - API testing
tags:
  - api testing
  - python
  - tavern
---
So far, most of the blog posts I've written that covered specific tools were focused on either Java or C#. Recently, though, I got a request for test automation training for a group of data science engineers, with the explicit requirement to use Python-based tools for the examples and exercises. 

Since then, I've been slowly expanding my reading and learning to also include the Python ecosystem, and I've also included a couple of Python-based test automation courses in <a href="https://www.ontestautomation.com/training/" target="_blank" rel="noreferrer noopener" aria-label="my training offerings (opens in a new tab)">my training offerings</a>. So far, I'm pretty impressed. There are plenty of powerful test tools available for Python, and in this post, I'd like to take a closer look at one of them, Tavern.

<a rel="noreferrer noopener" aria-label="Tavern (opens in a new tab)" href="https://taverntesting.github.io/" target="_blank">Tavern</a> is an API testing framework running on top of pytest, one of the most popular Python unit testing frameworks. It offers a range of features to write and run API tests, and if there's something you can't do with Tavern, it claims to be easily extensible through Python or pytest hooks and features. I can't vouch for its extensibility yet, thought, since all that I've been doing with Tavern so far was possible out of the box. Tavern has good <a rel="noreferrer noopener" aria-label="documentation (opens in a new tab)" href="https://tavern.readthedocs.io/en/latest/" target="_blank">documentation</a> too, which is also nice. 

Installing Tavern on your machine is easiest when done through <a rel="noreferrer noopener" aria-label="pip (opens in a new tab)" href="https://pypi.org/project/pip/" target="_blank">pip</a>, the Python package installer and manager using the command

`pip install -U tavern`

Tests in Tavern are written in YAML files. Now, you either love it or hate it, but it works. To get started, let's write a test that retrieves location data for the US zip code 90210 from the <a rel="noreferrer noopener" aria-label="Zippopotam.us API (opens in a new tab)" href="http://api.zippopotam.us/" target="_blank">Zippopotam.us API</a> and checks whether the response HTTP status code is equal to 200. This is what that looks like in Tavern:

{% highlight yaml %}
test_name: Get location for US zip code 90210 and check response status code

stages:
  - name: Check that HTTP status code equals 200
    request:
      url: http://api.zippopotam.us/us/90210
      method: GET
    response:
      status_code: 200
{% endhighlight %}

As I said, Tavern runs on top of pytest. So, to run this test, we need to invoke pytest and tell it that the tests we want to run are in the YAML file we created: 

![tavern status code](/images/blog/tavern_test_status_code.png "Output for a Tavern test on a status code")

As you can see, the test passes.

Another thing you might be interested in is checking values for specific response headers. Let's check that the response content type is equal to &#8216;application/json', telling the API consumer that they need to interpret the response as JSON:

{% highlight yaml %}
test_name: Get location for US zip code 90210 and check response content type

stages:
  - name: Check that content type equals application/json
    request:
      url: http://api.zippopotam.us/us/90210
      method: GET
    response:
      headers:
        content-type: application/json
{% endhighlight %}

Of course, you can also perform checks on the response body. Here's an example that checks that the place name associated with the aforementioned US zip code 90210 is equal to &#8216;Beverly Hills':

{% highlight yaml %}
test_name: Get location for US zip code 90210 and check response body content

stages:
  - name: Check that place name equals Beverly Hills
    request:
      url: http://api.zippopotam.us/us/90210
      method: GET
    response:
      body:
        places:
          - place name: Beverly Hills
{% endhighlight %}

Since APIs are all about data, you might want to repeat the same test more than once, but with different values for input parameters and expected outputs (i.e., do data driven testing). Tavern supports this too by exposing the pytest `parametrize` marker:

{% highlight yaml %}
test_name: Check place name for multiple combinations of country code and zip code

marks:
  - parametrize:
      key:
        - country_code
        - zip_code
        - place_name
      vals:
        - [us, 12345, Schenectady]
        - [ca, B2A, North Sydney South Central]
        - [nl, 3825, Vathorst]

stages:
  - name: Verify place name in response body
    request:
      url: http://api.zippopotam.us/{country_code}/{zip_code}
      method: GET
    response:
      body:
        places:
          - place name: "{place_name}"
{% endhighlight %}          

Even though we specified only a single test with a single stage, because we used the `parametrize` marker and supplied the test with three test data records, pytest effectively runs three tests (similar to what `@DataProvider` does in <a rel="noreferrer noopener" aria-label="TestNG (opens in a new tab)" href="https://testng.org/doc/documentation-main.html#parameters-dataproviders" target="_blank">TestNG</a> for Java, for example):

![Tavern data driven test](/images/blog/tavern_test_data_driven.png "Output of a data driven Tavern test") 

So far, we have only performed GET operations to retrieve data from an API provider, so we did not need to specify any request body contents. When, as an API consumer, you want to send data to an API provider, for example when you perform a POST or a PUT operation, you can do that like this using Tavern:

{% highlight yaml %}
test_name: Check response status code for a very simple addition API

stages:
  - name: Verify that status code equals 200 when two integers are specified
    request:
      url: http://localhost:5000/add
      json:
        first_number: 5
        second_number: 6
      method: POST
    response:
      status_code: 200
{% endhighlight %}

This test will POST a JSON document

{% highlight json %}
{"first_number": 5, "second_number": 6}
{% endhighlight %}

to the API provider running on localhost port 5000. Please note that for obvious reasons this test will fail when you run it yourself, unless you built an API or a mock that behaves in a way that makes the test pass (great exercise, different subject …).

So, that's it for a quick introduction to Tavern. I quite like the tool for its straightforwardness. What I'm still wondering is whether working with YAML will lead to maintainability and readability issues when you're working with larger test suites and larger request or response bodies. I'll keep working with Tavern in my training courses for now, so a follow-up blog post might see the light of day in a while!

All examples can be found on <a href="https://github.com/basdijkstra/ota-examples/tree/master/python-introduction-to-tavern" target="_blank" rel="noreferrer noopener" aria-label="this GitHub page (opens in a new tab)">this GitHub page</a>.