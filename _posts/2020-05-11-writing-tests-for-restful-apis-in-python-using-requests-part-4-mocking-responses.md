---
id: 2737
title: 'Writing tests for RESTful APIs in Python using requests – part 4: mocking responses'
date: 2020-05-11T10:35:21+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2737
permalink: /writing-tests-for-restful-apis-in-python-using-requests-part-4-mocking-responses/
spay_email:
  - ""
wp_featherlight_disable:
  - ""
categories:
  - API testing
tags:
  - python
  - requests
  - response mocking
  - unit testing
---
In this short series of blog posts, I want to explore the Python _<a href="https://requests.readthedocs.io/en/master/" target="_blank" rel="noreferrer noopener">requests</a>_ library and how it can be used for writing tests for RESTful APIs. This is the fourth blog post in the series, in which we will cover working mocking responses for unit testing purposes. Previous blog posts in this series talked about [getting started with requests and pytest](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-1-basic-tests/), about [creating data driven tests](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-2-data-driven-tests/) and about [working with XML-based APIs](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-3-working-with-xml/).

One thing that has been keeping me busy in the last couple of months is improving my software development skills. I&#8217;m currently working on a Python development project, and one of the tasks of a developer is writing good unit tests. When I was writing these tests, I ran into a challenge when I wanted to test a method that involves communicating with a REST API using the _requests_ library.

Obviously, I don&#8217;t want to have to invoke the API itself in my unit tests, so I was looking for a way to mock out that dependency instead. One thing I considered was writing mocks for the API myself, until I stumbled upon the _responses_ library (<a rel="noreferrer noopener" href="https://pypi.org/project/responses/" target="_blank">PyPI</a>, <a href="https://github.com/getsentry/responses" target="_blank" rel="noreferrer noopener">GitHub</a>). According to their homepage, this is &#8216;A utility library for mocking out the requests Python library&#8217;. Exactly what I was looking for.

So, what can you do with the responses library, and how can you use to your advantage when you&#8217;re writing unit tests? Let&#8217;s look at a couple of examples that involve creating mock responses for the <a href="http://api.zippopotam.us/" target="_blank" rel="noreferrer noopener">Zippopotam.us API</a>.

**Creating a mock response**  
Let&#8217;s say that in our unit test, we want to test that our code handles an HTTP 404 returned by a REST API dependency as expected. This implies we need a way to &#8216;override&#8217; the actual API response with a response that contains an HTTP 404 status code, and (maybe) a response body with an error message.

To use the _responses_ library to create such a mock response, you&#8217;ll first have to add the _@responses.activate_ decorator to your test method. In the test method body, you can then add a new mock response as follows:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">@responses.activate
def test_simulate_data_cannot_be_found():
    responses.add(
        responses.GET,
        &#039;http://api.zippopotam.us/us/90210&#039;,
        json={"error": "No data exists for US zip code 90210"},
        status=404
    )</pre>

When you use the _requests_ library to perform an HTTP GET to _http://api.zippopotam.us/us/90210_, instead of the response from the live API (which will return an HTTP 200), you&#8217;ll receive the mock response, instead, which we can confirm like this:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">response = requests.get(&#039;http://api.zippopotam.us/us/90210&#039;)
assert response.status_code == 404
response_body = response.json()
assert response_body[&#039;error&#039;] == &#039;No data exists for US zip code 90210&#039;</pre>

You can add any number of mock responses in this way.

**Unmapped responses**  
If, during testing, you accidentally hit an endpoint that does not have an associated mock response, you&#8217;ll get a _ConnectionError_:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">@responses.activate
def test_unmatched_endpoint_raises_connectionerror():
    with pytest.raises(ConnectionError):
        requests.get(&#039;http://api.zippopotam.us/us/12345&#039;)</pre>

**Simulating an exception being thrown**  
If you want to test how your code handles an exception being thrown when you perform an API call using _requests_, you can do that using _responses_, too:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">@responses.activate
def test_responses_can_raise_error_on_demand():
    responses.add(
        responses.GET,
        &#039;http://api.zippopotam.us/us/99999&#039;,
        body=RuntimeError(&#039;A runtime error occurred&#039;)
    )</pre>

You can confirm that this works as expected by asserting on the behaviour in a test:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">with pytest.raises(RuntimeError) as re:
    requests.get(&#039;http://api.zippopotam.us/us/99999&#039;)
assert str(re.value) == &#039;A runtime error occurred&#039;</pre>

**Creating dynamic responses**  
If you want to generate more complex and/or dynamic responses, you can do that by creating a callback and using that in your mock. This callback should return a tuple containing the response status code (an integer), the headers (a dictionary) and the response (in a string format).

In this example, I want to parse the request URL, extract the path parameters from it and then use those values in a message I return in the response body:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">@responses.activate
def test_using_a_callback_for_dynamic_responses():

    def request_callback(request):
        request_url = request.url
        resp_body = {&#039;value&#039;: generate_response_from(request_url)}
        return 200, {}, json.dumps(resp_body)

    responses.add_callback(
        responses.GET, &#039;http://api.zippopotam.us/us/55555&#039;,
        callback=request_callback,
        content_type=&#039;application/json&#039;,
    )

def generate_response_from(url):
    parsed_url = urlparse(url).path
    split_url = parsed_url.split(&#039;/&#039;)
    return f&#039;You requested data for {split_url[1].upper()} zip code {split_url[2]}&#039;</pre>

Again, writing a test confirms that this works as expected:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">response = requests.get(&#039;http://api.zippopotam.us/us/55555&#039;)
assert response.json() == {&#039;value&#039;: &#039;You requested data for US zip code 55555&#039;}</pre>

Plus, _responses_ retains all calls made to the callback and the responses it returned, which is very useful when you want to verify that your code made the correct (number of) calls:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">assert len(responses.calls) == 1
assert responses.calls[0].request.url == &#039;http://api.zippopotam.us/us/55555&#039;
assert responses.calls[0].response.text == &#039;{"value": "You requested data for US zip code 55555"}&#039;</pre>

**Using the examples for yourself**  
The code examples I have used in this blog post can be found on <a href="https://github.com/basdijkstra/ota-examples/tree/master/python-requests" target="_blank" rel="noreferrer noopener">my GitHub page</a>. If you download the project and (given you have installed Python properly) run

<pre class="wp-block-preformatted">pip install -r requirements.txt</pre>

from the root of the python-requests project to install the required libraries, you should be able to run the tests for yourself. See you next time!