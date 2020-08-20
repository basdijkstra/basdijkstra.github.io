---
title: 'Writing tests for RESTful APIs in Python using requests – part 1: basic tests'
layout: post
permalink: /writing-tests-for-restful-apis-in-python-using-requests-part-1-basic-tests/
categories:
  - API testing
tags:
  - api testing
  - python
  - requests
---
Recently, I've delivered my first ever three day ['Python for testers' training course](/training/python-for-testers/). One of the topics that was covered in this course is writing tests for RESTful APIs using the Python <a rel="noreferrer noopener" aria-label="requests (opens in a new tab)" href="https://requests.readthedocs.io/en/master/" target="_blank">requests</a> library and the <a href="https://docs.pytest.org/en/latest/" target="_blank" rel="noreferrer noopener" aria-label="pytest (opens in a new tab)">pytest</a> unit testing framework.

In this short series of blog posts, I want to explore the Python requests library and how it can be used for writing tests for RESTful APIs. This first blog post is all about getting started and writing our first tests against a sample REST API.

**Getting started**  
To get started, first we need a recent installation of the Python interpreter, which can be downloaded <a rel="noreferrer noopener" aria-label="here (opens in a new tab)" href="https://www.python.org/downloads/" target="_blank">here</a>. We then need to create a new project in our IDE (I use PyCharm, but any decent IDE works) and install the requests library. The easiest way to do this is using pip, the Python package manager:

`pip install -U requests`

Don't forget to create and activate a <a href="https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/" target="_blank" rel="noreferrer noopener" aria-label="virtual environment (opens in a new tab)">virtual environment</a> if you prefer that setup. We'll also need a unit testing framework to provide us with a test runner, an assertion library and some basic reporting functionality. I prefer pytest, but requests works equally well with other Python unit testing frameworks.

`pip install -U pytest`

Then, all we need to do to get started is to create a new Python file and import the requests library using

{% highlight python %}
import requests
{% endhighlight %}

**Our API under test**  
For the examples in this blog post, I'll be using the <a rel="noreferrer noopener" aria-label="Zippopotam.us (opens in a new tab)" href="http://api.zippopotam.us/" target="_blank">Zippopotam.us</a> REST API. This API takes a country code and a zip code and returns location data associated with that country and zip code. For example, a GET request to _http://api.zippopotam.us/us/90210_

returns an HTTP status code 200 and the following JSON response body:

{% highlight json %}
{
     "post code": "90210",
     "country": "United States",
     "country abbreviation": "US",
     "places": [
         {
             "place name": "Beverly Hills",
             "longitude": "-118.4065",
             "state": "California",
             "state abbreviation": "CA",
             "latitude": "34.0901"
         }
     ]
}
{% endhighlight %}

**A first test using requests and pytest**  
As a first test, let's use the requests library to invoke the API endpoint above and write an assertion that checks that the HTTP status code equals 200:

{% highlight python %}
def test_get_locations_for_us_90210_check_status_code_equals_200():
     response = requests.get("http://api.zippopotam.us/us/90210")
     assert response.status_code == 200
{% endhighlight %}

What's happening here? In the first line of the test, we call the _get()_ method in the requests library to perform an HTTP GET call to the specified endpoint, and we store the entire response in a variable called _response_. We then extract the _status_code_ property from the response object and write an assertion, using the pytest _assert_ keyword, that checks that the status code is equal to 200, as expected.

That's all there is to a first, and admittedly very basic, test against our API. Let's run this test and see what happens. I prefer to do this from the command line, because that's also how we will run the tests once they're part of an automated build pipeline. We can do so by calling pytest and telling it where to look for test files. Using the sample project referenced at the end of this blog post, and assuming we're in the project root folder, calling

`pytest tests\01_basic_tests.py`

results in the following console output:

![passing requests test](/images/blog/requests_test_pass.png "Passing requests test") 

It looks like our test is passing. Since I never trust a test I haven't seen fail (and neither should you), let's change the expected HTTP status code from 200 to 201 and see what happens:

![failing requests test](/images/blog/requests_test_fail.png "Failing requests test") 

That makes our test fail, as you can see. It looks like we're good to go with this one.

**Extending our test suite**  
Typically, we'll be interested in things other than the response HTTP status code, too. For example, let's check if the value of the response content type header correctly identifies that the response body is in JSON format:

{% highlight python %}
def test_get_locations_for_us_90210_check_content_type_equals_json():
     response = requests.get("http://api.zippopotam.us/us/90210")
     assert response.headers["Content-Type"] == "application/json"
{% endhighlight %}

In the response object, the headers are available as a dictionary (a list of key-value pairs) `headers`, which makes extracting the value for a specific header a matter of supplying the right key (the header name) to obtain its value. We can then assert on its value using a pytest assertion and the expected value of `application/json`.

How about checking the value of a response body element? Let's first check that the response body element `country` (see the sample JSON response above) is equal to `United States`:

{% highlight python %}
def test_get_locations_for_us_90210_check_country_equals_united_states():
     response = requests.get("http://api.zippopotam.us/us/90210")
     response_body = response.json()
     assert response_body["country"] == "United States"
{% endhighlight %}

The requests library comes with a built-in JSON decoder, which we can use to extract the response body from the response object and turn it into a proper JSON object. It is invoked using the `json()` method, which will raise a `ValueError` if there is no response body at all, as well as when the response is not valid JSON.

When we have decoded the response body into a JSON object, we can access elements in the body by referring to their name, in this case `country`.

To extract and assert on the value of the place name for the first place in the list of places, for example, we can do something similar:

{% highlight python %}
def test_get_locations_for_us_90210_check_city_equals_beverly_hills():
     response = requests.get("http://api.zippopotam.us/us/90210")
     response_body = response.json()
     assert response_body["places"][0]["place name"] == "Beverly Hills"
{% endhighlight %}

As a final example, let's check that the list of places returned by the API contains exactly one entry:

{% highlight python %}
def test_get_locations_for_us_90210_check_one_place_is_returned():
     response = requests.get("http://api.zippopotam.us/us/90210")
     response_body = response.json()
     assert len(response_body["places"]) == 1
{% endhighlight %}

This, too, is straightforward after we've converted the response body to JSON. The `len()` method that is built into Python returns the length of a list, in this case the list of items that is the value of the `places` element in the JSON document returned by the API.

In the next blog post, we're going to explore creating data driven tests using pytest and requests.

**Using the examples for yourself**  
The code examples I have used in this blog post can be found on <a rel="noreferrer noopener" aria-label="my GitHub page (opens in a new tab)" href="https://github.com/basdijkstra/ota-examples/tree/master/python-requests" target="_blank">my GitHub page</a>. If you download the project and (given you have installed Python properly) run

`pip install -r requirements.txt`

from the root of the `python-requests` project to install the required libraries, you should be able to run the tests for yourself. See you next time!