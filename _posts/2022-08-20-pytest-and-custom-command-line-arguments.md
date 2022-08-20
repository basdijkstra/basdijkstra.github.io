---
title: pytest and custom command line arguments
layout: post
permalink: /pytest-and-custom-command-line-arguments/
categories:
  - general
tags:
  - python
  - pytest
  - command line arguments
---
Last week, someone asked me the following question via email:

> Could you please suggest a good example of taking a parameter like "base environment URL" from the command line interface and passing it all the way to every test in a pytest-based test set?

From what I know, this is fairly easy to do in Java. Here's a command line example that uses Maven to run tests and passes in the value `http://localhost:8080/api` for a command line variable `env`:

```
mvn clean -Denv=http://localhost:8080/api test
```

In your Java code, you can then access the value for this variable using

{% highlight java %}
// 'http://some-default-env.com/api' will be used as the default if no value is specified for env
System.getProperty("env", "http://some-default-env.com/api");
{% endhighlight %}

But how to do this in pytest? And more importantly, how can we make sure that this value passed through the command line is available in all tests?

The answer: the pytest parser and pytest fixtures.

Let's have a step by step look.

### Registering a custom command line argument to pytest
The first step we need to take to enable us to pass command line arguments to pytest and use those in our tests is to register our new command line argument with pytest. This can be done by adding the following snippet to a `conftest.py` file that is put in the root folder of our project:

{% highlight python %}
def pytest_addoption(parser):
    parser.addoption(
        '--base-url', action='store', default='http://localhost:8080', help='Base URL for the API tests'
    )
{% endhighlight %}

This allows us to pass a custom argument `--base-url` when we invoke pytest, for example like this:

```
pytest --base-url=http://api.zippopotam.us zip_api_test.py
```

When we don't include the `--base-url` argument when we call pytest, the default value `http://localhost:8080` will be used.

The `parser` here is an object that's available to pytest by default, and that can be used to parse command line arguments as well as .ini file values.

### Using the command line argument value in our tests
Next, we need to read the command line argument value and pass it on to our tests. One of the most convenient ways to do this is by means of a [pytest fixture](https://docs.pytest.org/en/latest/explanation/fixtures.html){:target="_blank"}:

{% highlight python %}
@pytest.fixture
def base_url(request):
    return request.config.getoption('--base-url')
{% endhighlight %}

This fixture reads the value from the command line argument and returns it. We can now use this fixture in any of our tests like this:

{% highlight python %}
def test_api_endpoint(base_url):
    response = requests.get(f'{base_url}/us/90210')
    print(response.request.url)
    assert response.status_code == 200
{% endhighlight %}

This test method uses the `base_url` fixture to retrieve the base URL passed through the command line when pytest was invoked (or the default value when none was specified) to send an HTTP request to the endpoint using that base URL.

If we now run our test using

```pytest -s zip_api_test.py```

it will print

```http://localhost:8080/us/90210```

as the endpoint that was used for the HTTP call, since we did not specify any value for the `base-url` command line argument. If we invoke pytest using

```pytest -s --base-url=http://api.zippopotam.us zip_api_test.py```

it will print

```http://api.zippopotam.us/us/90210```

as the endpoint used in the request instead, showing that the value we passed using the command line argument was used successfully. Result!

### Expanding this approach to multiple variables
But what if we want to pass along multiple variables? What if, besides a base URL, we for instance also want to pass credentials to our tests, so that we don't have to store those in our test code?

As I see it, there are two ways of handling this. What you will have to do first in both cases is register the additional command line arguments, for example:

{% highlight python %}
def pytest_addoption(parser):
    parser.addoption(
        '--base-url', action='store', default='http://localhost:8080', help='Base URL for the API tests'
    )
    parser.addoption(
        '--username', action='store', default='test_user', help='Username to be used in the API tests'
    )
    parser.addoption(
        '--password', action='store', default='test_password', help='Password to be used in the API tests'
    )
{% endhighlight %}

Now, we have two options.

Option one: we store all command line argument values in a single object in our fixture, like this:

{% highlight python %}
@pytest.fixture
def command_line_args(request):
    args = {}
    args['base_url'] = request.config.getoption('--base-url')
    args['username'] = request.config.getoption('--username')
    args['password'] = request.config.getoption('--password')
    return args
{% endhighlight %}

We can then use this fixture in our tests and extract the values from the dictionary in our test method body.

Option two: we create a separate fixture for each of the command line arguments.

This keeps the fixtures small, but it requires you to use multiple fixtures in all of your tests, which can get a little verbose and doesn't really scale well. It does add some flexibility in deciding which fixture you want to use with which tests, though.

As with so many things in life, what approach works best will depend on the situation. In general, I think I prefer the first option, though. But maybe there's a third option that I haven't thought of yet? I would love to hear about that.