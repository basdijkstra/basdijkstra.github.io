---
title: 'Writing tests for RESTful APIs in Python using requests – part 2: data driven tests'
layout: post
permalink: /writing-tests-for-restful-apis-in-python-using-requests-part-2-data-driven-tests/
categories:
  - API testing
tags:
  - api testing
  - python
  - requests
---
Recently, I’ve delivered my first ever three day [‘Python for testers’ training course](/training/python-for-testers/). One of the topics that was covered in this course is writing tests for RESTful APIs using the Python <a href="https://requests.readthedocs.io/en/master/" target="_blank" rel="noreferrer noopener" aria-label="requests (opens in a new tab)">requests</a> library and the <a href="https://docs.pytest.org/en/latest/" target="_blank" rel="noreferrer noopener" aria-label="pytest (opens in a new tab)">pytest</a> unit testing framework.

In this short series of blog posts, I want to explore the Python requests library and how it can be used for writing tests for RESTful APIs. This is the second blog post in the series, in which we will cover writing data driven tests. The first blog post in the series can be found [here](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-1-basic-tests/).

**About data driven testing**  
Before we get started, let's quickly review what data driven tests are.

Often, when developing automated tests, you will find yourself wanting to test the same business logic, algorithm, computation or application flow multiple times with different input values and expected output values. Now, technically, you could achieve that by simply copying and pasting an existing test and changing the requires values.

From a maintainability perspective, however, that's not a good idea. Instead, you might want to consider writing a data driven test: a test that gets its test data from a data source and iterates over the rows (or records) in that data source.

**Creating a test data object**  
Most unit testing frameworks provide support for data driven testing, and pytest is no exception. Before we see how to create a data driven test in Python, let's create our test data source first. In Python, this can be as easy as creating a list of tuples, where each tuple in the list corresponds to an iteration of the data driven test (a 'test case', if you will).

{% highlight python %}
test_data_zip_codes = [
    ("us", "90210", "Beverly Hills"),
    ("ca", "B2A", "North Sydney South Central"),
    ("it", "50123", "Firenze")
{% endhighlight %}

We're going to run three iterations of the same test: retrieving location data for a given country code and zip code (the first two elements in each tuple) and then asserting that the corresponding place name returned by the API is equal to the specified expected place name (the third tuple element).

**Creating a data driven test in pytest**  
Now that we have our test data available, let's see how we can convert an existing test from the first blog post into a data driven test.

{% highlight python %}
@pytest.mark.parametrize("country_code, zip_code, expected_place_name", test_data_zip_codes)
def test_using_test_data_object_get_location_data_check_place_name(country_code, zip_code, expected_place_name):
    response = requests.get(f"http://api.zippopotam.us/{country_code}/{zip_code}")
    response_body = response.json()
    assert response_body["places"][0]["place name"] == expected_place_name
{% endhighlight %}

Pytest supports data driven testing through the built-in `@pytest.mark.parametrize` marker. This marker takes two arguments: the first tells pytest how (i.e., in which order) to map the elements in a tuple from the data source to the arguments of the test method, and the second argument is the test data object itself.

The test methods we have seen in the previous post did not have any arguments, but since we're feeding test data to our tests from outside, we need to specify three arguments to the test method here: the country code, the zip code and the expected place name. We can then use these arguments in our test method body, the first two as path parameter values in the API call, the last one as the expected result value which is extracted from the JSON response body.

**Running the test**  
When we run our data driven test, we see that even though we only have a single test method, pytest detects and runs three tests. Or better: it runs the same test three times, once for each tuple in the test data object.

![passing data driven test](/images/blog/data_driven_test_pass.png "Passing data driven test") 

This, to me, demonstrates the power of data driven testing. We can run as many iterations as required for a given test, without code duplication, given that we tell pytest where to find the test data. Need an additional test iteration with different test data values? Just add a record to the test data object. Want to update or remove a test case? You know the drill.

Another useful thing about data driven testing using pytest: when one of the test iterations fails, pytest will tell you which one did and what were the corresponding test data values used:

![failing data driven test](/images/blog/data_driven_test_fail.png "Failing data driven test") 

**Creating an external data source**  
In the example above, our test data was still hardcoded into our test code. This might not be your preferred way of working. What if we could specify the test data in an external data source instead, and tell pytest to read it from there?

As an example, let's create a .csv file that contains the same test data as the test data object we've seen earlier:

{% highlight text %}
country_code,zip_code,expected_place_name
us,90210,Beverly Hills
ca,B2A,North Sydney South Central
it,50123,Firenze
{% endhighlight %}

To use this test data in our test, we need to write a Python method that reads the data from the file and returns it in a format that's compatible with the pytest parametrize marker. Python offers solid support for handling .csv files in the built-in _csv_ library:

{% highlight python %}
import csv

def read_test_data_from_csv():
    test_data = []
    with open("test_data/test_data_zip_codes.csv", newline="") as csvfile:
        data = csv.reader(csvfile, delimiter=",")
        next(data)  # skip header row
        for row in data:
            test_data.append(row)
    return test_data
{% endhighlight %}

This method opens the .csv file in reading mode, skips the header row, adds all other lines to the list of test data values test_data one by one and returns the test data object.

The test method itself now needs to be updated to not use the hardcoded test data object anymore, but instead use the return value of the method that reads the data from the .csv file:

{% highlight python %}
@pytest.mark.parametrize("country_code, zip_code, expected_place_name", read_test_data_from_csv())
def test_using_csv_get_location_data_check_place_name(country_code, zip_code, expected_place_name):
    response = requests.get(f"http://api.zippopotam.us/{country_code}/{zip_code}")
    response_body = response.json()
    assert response_body["places"][0]["place name"] == expected_place_name
{% endhighlight %}

Running this updated test code will show that this approach, too, results in three passing test iterations. Of course, you can use test data sources other than .csv too, such as database query results or XML or JSON files. As long as you're able to write a method that returns a list of test data value tuples, you should be good to go.

In the next blog post, we’re going to further explore working with JSON and XML in API request and response bodies.

**Using the examples for yourself**  
The code examples I have used in this blog post can be found on <a href="https://github.com/basdijkstra/ota-examples/tree/master/python-requests" target="_blank" rel="noreferrer noopener" aria-label="my GitHub page (opens in a new tab)">my GitHub page</a>. If you download the project and (given you have installed Python properly) run

`pip install -r requirements.txt`

from the root of the python-requests project to install the required libraries, you should be able to run the tests for yourself. See you next time!