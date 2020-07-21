---
id: 2614
title: 'Writing tests for RESTful APIs in Python using requests – part 3: working with XML'
date: 2020-01-10T09:11:58+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2614
permalink: /writing-tests-for-restful-apis-in-python-using-requests-part-3-working-with-xml/
spay_email:
  - ""
categories:
  - API testing
tags:
  - api testing
  - python
  - requests
---
Recently, I’ve delivered my first ever three day ‘[Python for testers](https://www.ontestautomation.com/training/python-for-testers/)’ training course. One of the topics that was covered in this course is writing tests for RESTful APIs using the Python <a href="https://requests.readthedocs.io/en/master/" target="_blank" rel="noreferrer noopener" aria-label="requests (opens in a new tab)">requests</a> library and the <a href="https://docs.pytest.org/en/latest/" target="_blank" rel="noreferrer noopener" aria-label="pytest (opens in a new tab)">pytest</a> unit testing framework.

In this short series of blog posts, I want to explore the Python requests library and how it can be used for writing tests for RESTful APIs. This is the third blog post in the series, in which we will cover working with XML request and response bodies. Previous blog posts in this series talked about [getting started with requests and pytest](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-1-basic-tests/), and [about creating data driven tests](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-2-data-driven-tests/).

**REST APIs and XML**  
While most REST APIs I encounter nowadays work with JSON as the preferred data format for request and response body bodies, from time to time you'll encounter APIs that work with XML. And since XML is a little more cumbersome to work with XML in code compared to JSON (not just in Python, but in general), I thought it would be a good idea to show you some examples of how to create XML request bodies and how to parse and assert on XML response bodies when you're working with the requests library.

For the examples in this blog post, I'll be using an operation from the <a href="http://parabank.parasoft.com/parabank/api-docs/index.html" target="_blank" rel="noreferrer noopener" aria-label="ParaBank REST API (opens in a new tab)">ParaBank REST API</a> that can be used to submit bill payments. It's available at

`<http://parabank.parasoft.com/parabank/services/bank/billpay>`

and takes, next to two query parameters specifying the source `accountId` and the bill `amount`, an XML request body containing specifics about the person to whom the payment is sent, i.e., the `payee`. Not surprisingly, this request body is sent to the API provider using an HTTP POST.

**Creating XML request bodies using strings**  
I'd like to show you two distinct approaches to creating XML request bodies. The first one is the most straightforward one, but also the least flexible: creating a method that returns a string object containing the XML body:

{% highlight python %}
def fixed_xml_body_as_string():
    return """
    <payee>
        <name>John Smith</name>
        <address>
            <street>My street</street>
            <city>My city</city>
            <state>My state</state>
            <zipCode>90210</zipCode>
        </address>
        <phoneNumber>0123456789</phoneNumber>
        <accountNumber>12345</accountNumber>
    </payee>
    """
{% endhighlight %}

Note the use of the triple double quotes to allow you to declare a multi-line string. Of course, instead of hard-coding our XML request body in our code, we could also read it from an XML (or text) file stored somewhere on our file system. The result is the same.

If we want to pass this XML request body to our API, we can do that like this:

{% highlight python %}
def test_send_xml_body_from_string_check_status_code_and_content_type():
    response = requests.post(
        "http://parabank.parasoft.com/parabank/services/bank/billpay?accountId=12345&amount=500",
        headers={"Content-Type": "application/xml"},
        data=fixed_xml_body_as_string()
    )
    assert response.status_code == 200
    assert response.headers["Content-Type"] == "application/xml"
{% endhighlight %}

Note that we explicitly set the `Content-Type` header of the request to `application/xml` to make sure the provider understands that the request body should be interpreted as XML. Sending the XML request body is done by assigning the return value of our method returning the XML as a string to the `data` parameter of the requests `post()` method.

To check that our request has been received and processed successfully, we assert that the response status code equals 200 and that the response `Content-Type` header has a value of `application/xml`. We'll take a closer look at the actual XML response body later on in this post.

**Creating XML request bodies using ElementTree**  
The other approach to working with XML request bodies is to programmatically build them. Python contains a powerful library to do this, called _<a rel="noreferrer noopener" aria-label="ElementTree (opens in a new tab)" href="https://docs.python.org/3/library/xml.etree.elementtree.html" target="_blank">ElementTree</a>_. We can import this into our module using

{% highlight python %}
import xml.etree.ElementTree as et
{% endhighlight %}

Since an XML document is essentially a tree with a root node with child nodes attached to it, we start creating our XML request body by defining the _payee_ root node:

{% highlight python %}
payee = et.Element("payee")
{% endhighlight %}

We can then define an element `name` that is a child element of `payee`:

{% highlight python %}
name = et.SubElement(payee, "name")
{% endhighlight %}

We also need to assign an element value to the `name` element:

{% highlight python %}
name.text = "John Smith"
{% endhighlight %}

It's not required for this example, but if you would have to add an attribute, say, `type`, with value `fullName` to the `name` element, you could do so like this:

{% highlight python %}
name.set("type", "fullName")
{% endhighlight %}

Creating the entire XML request body for our API call is a matter of repeating the above statements in the right order, with the right values:

{% highlight python %}
def create_xml_body_using_elementtree():
    payee = et.Element("payee")
    name = et.SubElement(payee, "name")
    name.text = "John Smith"
    address = et.SubElement(payee, "address")
    street = et.SubElement(address, "street")
    street.text = "My street"
    city = et.SubElement(address, "city")
    city.text = "My city"
    state = et.SubElement(address, "state")
    state.text = "My state"
    zip_code = et.SubElement(address, "zipCode")
    zip_code.text = "90210"
    phone_number = et.SubElement(payee, "phoneNumber")
    phone_number.text = "0123456789"
    account_number = et.SubElement(payee, "accountNumber")
    account_number.text = "12345"
    return et.tostring(payee)
{% endhighlight %}

Note that we need to convert the element tree into a string before we can use it with the requests library. We can do this using the `tostring()` method.

While the approach using ElementTree might look a little more cumbersome than simply specifying our XML as a string, it gives us the option of creating more complex and flexible XML documents by creating loops to repeat XML blocks, working with data sources that are transformed into XML, and so on. I myself don't really prefer one approach over the other, but I think it's good to be aware of both and choose the one that best fits your requirements.

If we want to use the XML created using ElementTree above as a request body, we can do that in exactly the same way as when we used a string containing the XML:

{% highlight python %}
def test_send_xml_body_from_elementtree_check_status_code_and_content_type():
    response = requests.post(
        "http://parabank.parasoft.com/parabank/services/bank/billpay?accountId=12345&amount=500",
        headers={"Content-Type": "application/xml"},
        data=create_xml_body_using_elementtree()
    )
    assert response.status_code == 200
    assert response.headers["Content-Type"] == "application/xml"
{% endhighlight %}    

**Parsing and working with XML response bodies**  
Now that we have covered creating XML request bodies, let's see what we can do with XML responses. By far the most powerful way to create specific assertions is to convert the XML response body into an ElementTree and then asserting on its properties.

As an example, we're going to perform an HTTP GET call to

`<http://parabank.parasoft.com/parabank/services/bank/accounts/12345>`

which returns details of the account with ID 12345. If we want to assert, for example, that the root node of the XML response is named `account`, and that it has neither any attributes nor a text value, we can do this as follows:

{% highlight python %}
def test_check_root_of_xml_response():
    response = requests.get("http://parabank.parasoft.com/parabank/services/bank/accounts/12345")
    response_body_as_xml = et.fromstring(response.content)
    xml_tree = et.ElementTree(response_body_as_xml)
    root = xml_tree.getroot()
    assert root.tag == "account"
    assert len(root.attrib) == 0
    assert root.text is None
{% endhighlight %}

Note that we first have to convert the XML response body to an object of type `Element` using the `fromstring()` method, then create an `ElementTree` out of that using the `ElementTree()` constructor, which takes an `Element` as its argument.

If we're interested in the text value of a specific subelement of the XML response, for example `customerId` which contains the ID of the customer to whom this account belongs, we can do that by finding it in the ElementTree using the `find()` method, then write an assertion on the `text` property of the found element:

{% highlight python %}
def test_check_specific_element_of_xml_response():
    response = requests.get("http://parabank.parasoft.com/parabank/services/bank/accounts/12345")
    response_body_as_xml = et.fromstring(response.content)
    xml_tree = et.ElementTree(response_body_as_xml)
    first_name = xml_tree.find("customerId")
    assert first_name.text == "12212"
{% endhighlight %}

It's good to know that the `find()` method returns the first occurrence of a specific element. If we want to return all elements that match a specific name, we need to use `findall()` instead:

{% highlight python %}
def test_use_xpath_for_more_sophisticated_checks():
    response = requests.get("http://parabank.parasoft.com/parabank/services/bank/customers/12212/accounts")
    response_body_as_xml = et.fromstring(response.content)
    xml_tree = et.ElementTree(response_body_as_xml)
    savings_accounts = xml_tree.findall(".//account/type[.="SAVINGS"]")
    assert len(savings_accounts) > 1
{% endhighlight %}   

As you can see, next to passing element names directly, we can also use XPath expressions to perform more sophisticated selections. The expression

`.//account/type[.="SAVINGS"]`

in the example above selects all occurrences of the `type` element (a child element of `account`) that have `SAVINGS` as their element value.

**Using the examples for yourself**  
The code examples I have used in this blog post can be found on <a href="https://github.com/basdijkstra/ota-examples/tree/master/python-requests" target="_blank" rel="noreferrer noopener" aria-label="my GitHub page (opens in a new tab)">my GitHub page</a>. If you download the project and (given you have installed Python properly) run

`pip install -r requirements.txt`

from the root of the python-requests project to install the required libraries, you should be able to run the tests for yourself. See you next time!