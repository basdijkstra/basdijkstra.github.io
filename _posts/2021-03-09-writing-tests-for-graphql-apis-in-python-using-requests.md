---
title: Writing tests for GraphQL APIs in Python using requests
layout: post
permalink: /writing-tests-for-graphql-apis-in-python-using-requests/
categories:
  - API testing
tags:
  - python
  - requests
  - graphql
---
Last week I delivered a 2-day version of my [API testing masterclass](https://www.ontestautomation.com/training/api-testing-masterclass/) in Python. While we were going through some exercises in writing automated tests for RESTful APIs with [requests](https://pypi.org/project/requests/){:target="_blank"}, one of the participants asked if we could apply what we've learned to testing GraphQL APIs as well as 'regular' REST APIs. Now, I'd never actually done this before, but after a bit of mob Googling and discussion we concluded that this shouldn't be too much of a problem.

As this discussion took place somewhere during the first day of the course, I promised the participants to prepare a couple of quick exercises that they could work on during the second day. In this blog post, you'll see some of the examples I included in those exercises.

One of the participants came up with a great example to use as our API under test: the [SpaceX GraphQL API](https://api.spacex.land/graphql/){:target="_blank"}. This public API contains data about the SpaceX company, as well as their history, space fleet, missions, launches and more. Plenty of data to work with.

For those of you not familiar with GraphQL APIs, there's a difference in the way you retrieve data from it compared to 'regular' REST APIs. With regular REST APIs, you typically send a GET request to retrieve the data associated with a specific resource. For example, a GET to `http://api.zippopotam.us/us/90210` retrieves location data associated with the US zip code 90210.

With GraphQL queries, on the other hand, you construct a GraphQL query and POST that to the API. This enables you to retrieve data for multiple resources, potentially from multiple data sources, in a single, aggregated API call.

For example, if we want to retrieve the SpaceX company name, as well as the names of its CEO and COO, we could do that by posting the following GraphQL query to https://api.spacex.land/graphql/:

{% highlight json%}
{
    company {
        ceo
        coo
        name
    }
}
{% endhighlight %}

This returns a response with the following JSON response body content:

{% highlight json %}
{
    "data": {
        "company": {
            "ceo": "Elon Musk",
            "coo": "Gwynne Shotwell",
            "name": "SpaceX"
        }
    }
}
{% endhighlight %}

As you can see, while the query being posted to the request is in GraphQL-specific syntax, the response is plain JSON, which is good news if you want to use a library like requests to write tests for GraphQL APIs. Let's see how we can do exactly that.

The most straightforward way to create and send a GraphQL query to an API is by hard-coding the query into our test code as a (multiline) string:

{% highlight python %}
query_company_ceo_coo_name = """
{
    company {
        ceo
        coo
        name
    }
}
"""
{% endhighlight %}

All we really need to do then to send it to our GraphQL API is to create a JSON request payload with a single element `query` with our query string as its value. That's all there is to it, really. Here's an example of what that looks like in a test using requests and pytest, complete with an assertion on the response HTTP status code to check that our request was processed successfully:

{% highlight python %}
def test_retrieve_graphql_data_should_yield_http_200():
    response = requests.post("https://api.spacex.land/graphql/", json={'query': query_company_ceo_coo_name})
    assert response.status_code == 200
{% endhighlight %}

And since the response body is regular JSON, we can assert on specific response body element values by converting the JSON to a Python dictionary first, using the `json()` method, and then accessing the element we're interested in and checking its value against our expectation. Here's an example that checks that the name of the SpaceX company CEO is equal to 'Elon Musk':

{% highlight python %}
def test_retrieve_graphql_data_should_yield_ceo_elon_musk():
    response = requests.post("https://api.spacex.land/graphql/", json={'query': query_company_ceo_coo_name})
    response_body = response.json()
    assert response_body['data']['company']['ceo'] == 'Elon Musk'
{% endhighlight %}

Well, that was surprisingly straightforward. The only thing that bothers me here is the fact that we're still hard-coding our GraphQL queries as multiline strings. Surely, there's a way to create and submit these queries programmatically, too, right? Well, I'm glad you asked, because yes, there is a way to do that. We'll need the help of an additional library, though.

[sgqlc](https://pypi.org/project/sgqlc/){:target="_blank"} is a Python GraphQL client that allows us to define classes that represent our GraphQL queries, which can then be submitted to a GraphQL API just as we did in the previous examples. Let's try to create the following GraphQL query (retrieving the ID, name and description for the various SpaceX missions) in code:

{% highlight json %}
{
    missions {
        id
        name
        description
    }
}
{% endhighlight %}

To do that, we are first going to build a class that represents an individual mission node:

{% highlight python %}
from sgqlc.types import String, Type

class MissionNode(Type):
    id = String
    name = String
    description = String
{% endhighlight %}

We're interested in three fields for a mission, as previously mentioned these are the `id`, `name` and `description`. The value of each of these fields is a `String`.

After that, we'll build our actual query from this `MissionNode` type like this:

{% highlight python %}
from sgqlc.types import Field

class Query(Type):
    missions = Field(MissionNode)
{% endhighlight %}

And that's it, really. We now have a data structure that we can use to create an actual GraphQL query and send that to our API. Here's how to use this data structure in a test:

{% highlight python %}
from sgqlc.operation import Operation

def test_create_graphql_query_programmatically():
    query = Operation(Query)
    query.missions()

    response = requests.post("https://api.spacex.land/graphql/", json={'query': str(query)})
    assert response.status_code == 200
    assert len(response.json()['data']['missions']) == 10
{% endhighlight %}

First, we create a new instance of our `Query` class using `query = Operation(Query)`. We then add our `MissionNode` selection fields to the query using `query.missions()`, which creates the exact GraphQL query we want to POST. Then, all we need to do is add our query object as a JSON payload to our POST request, and we're good to go. Don't forget to send the string representation of the query using `str()` instead of the object itself in the payload, otherwise it will not work!

So, there you have it. As you can see, starting to write tests for GraphQL APIs with Python and requests isn't much different from writing tests for 'regular' REST APIs. You can find all the examples used in this blog post on [my GitHub page](https://github.com/basdijkstra/ota-examples/tree/master/python-requests-graphql){:target="_blank"}.