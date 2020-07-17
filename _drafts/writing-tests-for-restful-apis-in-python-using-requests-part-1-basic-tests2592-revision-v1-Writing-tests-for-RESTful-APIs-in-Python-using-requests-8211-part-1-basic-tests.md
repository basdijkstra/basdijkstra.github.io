---
id: 2602
title: 'Writing tests for RESTful APIs in Python using requests &#8211; part 1: basic tests'
date: 2019-12-16T10:26:25+01:00
author: Bas
layout: revision
guid: https://www.ontestautomation.com/2592-revision-v1/
permalink: /2592-revision-v1/
---
Recently, I&#8217;ve delivered my first ever three day [&#8216;Python for testers&#8217; training course](https://www.ontestautomation.com/training/python-for-testers/). One of the topics that was covered in this course is writing tests for RESTful APIs using the Python <a rel="noreferrer noopener" aria-label="requests (opens in a new tab)" href="https://requests.readthedocs.io/en/master/" target="_blank">requests</a> library and the <a href="https://docs.pytest.org/en/latest/" target="_blank" rel="noreferrer noopener" aria-label="pytest (opens in a new tab)">pytest</a> unit testing framework.

In this short series of blog posts, I want to explore the Python requests library and how it can be used for writing tests for RESTful APIs. This first blog post is all about getting started and writing our first tests against a sample REST API.

**Getting started**  
To get started, first we need a recent installation of the Python interpreter, which can be downloaded <a rel="noreferrer noopener" aria-label="here (opens in a new tab)" href="https://www.python.org/downloads/" target="_blank">here</a>. We then need to create a new project in our IDE (I use PyCharm, but any decent IDE works) and install the requests library. The easiest way to do this is using pip, the Python package manager:

<pre class="wp-block-preformatted">pip install -U requests </pre>

Don&#8217;t forget to create and activate a <a href="https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/" target="_blank" rel="noreferrer noopener" aria-label="virtual environment (opens in a new tab)">virtual environment</a> if you prefer that setup. We&#8217;ll also need a unit testing framework to provide us with a test runner, an assertion library and some basic reporting functionality. I prefer pytest, but requests works equally well with other Python unit testing frameworks.

<pre class="wp-block-preformatted">pip install -U pytest </pre>

Then, all we need to do to get started is to create a new Python file and import the requests library using

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">import requests</pre>

**Our API under test**  
For the examples in this blog post, I&#8217;ll be using the <a rel="noreferrer noopener" aria-label="Zippopotam.us (opens in a new tab)" href="http://api.zippopotam.us/" target="_blank">Zippopotam.us</a> REST API. This API takes a country code and a zip code and returns location data associated with that country and zip code. For example, a GET request to _http://api.zippopotam.us/us/90210_

returns an HTTP status code 200 and the following JSON response body:

<pre class="EnlighterJSRAW" data-enlighter-language="json" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">{
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
 }</pre>

**A first test using requests and pytest**  
As a first test, let&#8217;s use the requests library to invoke the API endpoint above and write an assertion that checks that the HTTP status code equals 200:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_get_locations_for_us_90210_check_status_code_equals_200():
     response = requests.get("http://api.zippopotam.us/us/90210")
     assert response.status_code == 200</pre>

What&#8217;s happening here? In the first line of the test, we call the _get()_ method in the requests library to perform an HTTP GET call to the specified endpoint, and we store the entire response in a variable called _response_. We then extract the _status_code_ property from the response object and write an assertion, using the pytest _assert_ keyword, that checks that the status code is equal to 200, as expected.

That&#8217;s all there is to a first, and admittedly very basic, test against our API. Let&#8217;s run this test and see what happens. I prefer to do this from the command line, because that&#8217;s also how we will run the tests once they&#8217;re part of an automated build pipeline. We can do so by calling pytest and telling it where to look for test files. Using the sample project referenced at the end of this blog post, and assuming we&#8217;re in the project root folder, calling

<pre class="wp-block-preformatted">pytest tests\01_basic_tests.py </pre>

results in the following console output:<figure class="wp-block-image size-full">

<img src="https://www.ontestautomation.com/wp-content/uploads/2019/12/01_passing_pytest_test.png" alt="Console output showing a passing test." class="wp-image-2595" srcset="https://www.ontestautomation.com/wp-content/uploads/2019/12/01_passing_pytest_test.png 919w, https://www.ontestautomation.com/wp-content/uploads/2019/12/01_passing_pytest_test-300x83.png 300w, https://www.ontestautomation.com/wp-content/uploads/2019/12/01_passing_pytest_test-768x213.png 768w" sizes="(max-width: 919px) 100vw, 919px" /> </figure> 

It looks like our test is passing. Since I never trust a test I haven&#8217;t seen fail (and neither should you), let&#8217;s change the expected HTTP status code from 200 to 201 and see what happens:<figure class="wp-block-image size-large">

<img src="https://www.ontestautomation.com/wp-content/uploads/2019/12/02_failing_pytest_test.png" alt="Console output showing a failing test." class="wp-image-2596" srcset="https://www.ontestautomation.com/wp-content/uploads/2019/12/02_failing_pytest_test.png 920w, https://www.ontestautomation.com/wp-content/uploads/2019/12/02_failing_pytest_test-300x158.png 300w, https://www.ontestautomation.com/wp-content/uploads/2019/12/02_failing_pytest_test-768x404.png 768w" sizes="(max-width: 920px) 100vw, 920px" /> </figure> 

That makes our test fail, as you can see. It looks like we&#8217;re good to go with this one.

**Extending our test suite**  
Typically, we&#8217;ll be interested in things other than the response HTTP status code, too. For example, let&#8217;s check if the value of the response content type header correctly identifies that the response body is in JSON format:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_get_locations_for_us_90210_check_content_type_equals_json():
     response = requests.get("http://api.zippopotam.us/us/90210")
     assert response.headers[&#039;Content-Type&#039;] == "application/json"</pre>

In the response object, the headers are available as a dictionary (a list of key-value pairs) _headers_, which makes extracting the value for a specific header a matter of supplying the right key (the header name) to obtain its value. We can then assert on its value using a pytest assertion and the expected value of &#8216;_application/json_&#8216;.

How about checking the value of a response body element? Let&#8217;s first check that the response body element _country_ (see the sample JSON response above) is equal to _United States_:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_get_locations_for_us_90210_check_country_equals_united_states():
     response = requests.get("http://api.zippopotam.us/us/90210")
     response_body = response.json()
     assert response_body["country"] == "United States"</pre>

The requests library comes with a built-in JSON decoder, which we can use to extract the response body from the response object and turn it into a proper JSON object. It is invoked using the _json()_ method, which will raise a _ValueError_ if there is no response body at all, as well as when the response is not valid JSON.

When we have decoded the response body into a JSON object, we can access elements in the body by referring to their name, in this case _country_.

To extract and assert on the value of the place name for the first place in the list of places, for example, we can do something similar:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_get_locations_for_us_90210_check_city_equals_beverly_hills():
     response = requests.get("http://api.zippopotam.us/us/90210")
     response_body = response.json()
     assert response_body["places"][0]["place name"] == "Beverly Hills"</pre>

As a final example, let&#8217;s check that the list of places returned by the API contains exactly one entry:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_get_locations_for_us_90210_check_one_place_is_returned():
     response = requests.get("http://api.zippopotam.us/us/90210")
     response_body = response.json()
     assert len(response_body["places"]) == 1</pre>

This, too, is straightforward after we&#8217;ve converted the response body to JSON. The _len()_ method that is built into Python returns the length of a list, in this case the list of items that is the value of the _places_ element in the JSON document returned by the API.

In the next blog post, we&#8217;re going to explore creating data driven tests using pytest and requests.

**Using the examples for yourself**  
The code examples I have used in this blog post can be found on <a rel="noreferrer noopener" aria-label="my GitHub page (opens in a new tab)" href="https://github.com/basdijkstra/ota-examples/tree/master/python-requests" target="_blank">my GitHub page</a>. If you download the project and (given you have installed Python properly) run

<pre class="wp-block-preformatted">pip install -r requirements.txt </pre>

from the root of the _python-requests_ project to install the required libraries, you should be able to run the tests for yourself. See you next time!