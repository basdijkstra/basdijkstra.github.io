---
id: 2620
title: 'Writing tests for RESTful APIs in Python using requests – part 3: working with XML'
date: 2020-01-10T09:11:58+01:00
author: Bas
layout: revision
guid: https://www.ontestautomation.com/2614-revision-v1/
permalink: /2614-revision-v1/
---
Recently, I’ve delivered my first ever three day ‘[Python for testers](https://www.ontestautomation.com/training/python-for-testers/)’ training course. One of the topics that was covered in this course is writing tests for RESTful APIs using the Python <a href="https://requests.readthedocs.io/en/master/" target="_blank" rel="noreferrer noopener" aria-label="requests (opens in a new tab)">requests</a> library and the <a href="https://docs.pytest.org/en/latest/" target="_blank" rel="noreferrer noopener" aria-label="pytest (opens in a new tab)">pytest</a> unit testing framework.

In this short series of blog posts, I want to explore the Python requests library and how it can be used for writing tests for RESTful APIs. This is the third blog post in the series, in which we will cover working with XML request and response bodies. Previous blog posts in this series talked about [getting started with requests and pytest](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-1-basic-tests/), and [about creating data driven tests](https://www.ontestautomation.com/writing-tests-for-restful-apis-in-python-using-requests-part-2-data-driven-tests/).

**REST APIs and XML**  
While most REST APIs I encounter nowadays work with JSON as the preferred data format for request and response body bodies, from time to time you&#8217;ll encounter APIs that work with XML. And since XML is a little more cumbersome to work with XML in code compared to JSON (not just in Python, but in general), I thought it would be a good idea to show you some examples of how to create XML request bodies and how to parse and assert on XML response bodies when you&#8217;re working with the requests library.

For the examples in this blog post, I&#8217;ll be using an operation from the <a href="http://parabank.parasoft.com/parabank/api-docs/index.html" target="_blank" rel="noreferrer noopener" aria-label="ParaBank REST API (opens in a new tab)">ParaBank REST API</a> that can be used to submit bill payments. It&#8217;s available at

<http://parabank.parasoft.com/parabank/services/bank/billpay>

and takes, next to two query parameters specifying the source _accountId_ and the bill _amount_, an XML request body containing specifics about the person to whom the payment is sent, i.e., the _payee_. Not surprisingly, this request body is sent to the API provider using an HTTP POST.

**Creating XML request bodies using strings**  
I&#8217;d like to show you two distinct approaches to creating XML request bodies. The first one is the most straightforward one, but also the least flexible: creating a method that returns a string object containing the XML body:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def fixed_xml_body_as_string():
    return """
    &lt;payee&gt;
        &lt;name&gt;John Smith&lt;/name&gt;
        &lt;address&gt;
            &lt;street&gt;My street&lt;/street&gt;
            &lt;city&gt;My city&lt;/city&gt;
            &lt;state&gt;My state&lt;/state&gt;
            &lt;zipCode&gt;90210&lt;/zipCode&gt;
        &lt;/address&gt;
        &lt;phoneNumber&gt;0123456789&lt;/phoneNumber&gt;
        &lt;accountNumber&gt;12345&lt;/accountNumber&gt;
    &lt;/payee&gt;
    """</pre>

Note the use of the triple double quotes to allow you to declare a multi-line string. Of course, instead of hard-coding our XML request body in our code, we could also read it from an XML (or text) file stored somewhere on our file system. The result is the same.

If we want to pass this XML request body to our API, we can do that like this:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_send_xml_body_from_string_check_status_code_and_content_type():
    response = requests.post(
        "http://parabank.parasoft.com/parabank/services/bank/billpay?accountId=12345&amount=500",
        headers={"Content-Type": "application/xml"},
        data=fixed_xml_body_as_string()
    )
    assert response.status_code == 200
    assert response.headers["Content-Type"] == "application/xml"</pre>

Note that we explicitly set the _Content-Type_ header of the request to _application/xml_ to make sure the provider understands that the request body should be interpreted as XML. Sending the XML request body is done by assigning the return value of our method returning the XML as a string to the _data_ parameter of the requests _post()_ method.

To check that our request has been received and processed successfully, we assert that the response status code equals 200 and that the response _Content-Type_ header has a value of _application/xml_. We&#8217;ll take a closer look at the actual XML response body later on in this post.

**Creating XML request bodies using ElementTree**  
The other approach to working with XML request bodies is to programmatically build them. Python contains a powerful library to do this, called _<a rel="noreferrer noopener" aria-label="ElementTree (opens in a new tab)" href="https://docs.python.org/3/library/xml.etree.elementtree.html" target="_blank">ElementTree</a>_. We can import this into our module using

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">import xml.etree.ElementTree as et</pre>

Since an XML document is essentially a tree with a root node with child nodes attached to it, we start creating our XML request body by defining the _payee_ root node:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">payee = et.Element(&#039;payee&#039;)</pre>

We can then define an element _name_ that is a child element of _payee_:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">name = et.SubElement(payee, &#039;name&#039;)</pre>

We also need to assign an element value to the _name_ element:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">name.text = &#039;John Smith&#039;</pre>

It&#8217;s not required for this example, but if you would have to add an attribute, say, _type_, with value _fullName_ to the _name_ element, you could do so like this:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">name.set(&#039;type&#039;, &#039;fullName&#039;)</pre>

Creating the entire XML request body for our API call is a matter of repeating the above statements in the right order, with the right values:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def create_xml_body_using_elementtree():
    payee = et.Element(&#039;payee&#039;)
    name = et.SubElement(payee, &#039;name&#039;)
    name.text = &#039;John Smith&#039;
    address = et.SubElement(payee, &#039;address&#039;)
    street = et.SubElement(address, &#039;street&#039;)
    street.text = &#039;My street&#039;
    city = et.SubElement(address, &#039;city&#039;)
    city.text = &#039;My city&#039;
    state = et.SubElement(address, &#039;state&#039;)
    state.text = &#039;My state&#039;
    zip_code = et.SubElement(address, &#039;zipCode&#039;)
    zip_code.text = &#039;90210&#039;
    phone_number = et.SubElement(payee, &#039;phoneNumber&#039;)
    phone_number.text = &#039;0123456789&#039;
    account_number = et.SubElement(payee, &#039;accountNumber&#039;)
    account_number.text = &#039;12345&#039;
    return et.tostring(payee)</pre>

Note that we need to convert the element tree into a string before we can use it with the requests library. We can do this using the _tostring()_ method.

While the approach using ElementTree might look a little more cumbersome than simply specifying our XML as a string, it gives us the option of creating more complex and flexible XML documents by creating loops to repeat XML blocks, working with data sources that are transformed into XML, and so on. I myself don&#8217;t really prefer one approach over the other, but I think it&#8217;s good to be aware of both and choose the one that best fits your requirements.

If we want to use the XML created using ElementTree above as a request body, we can do that in exactly the same way as when we used a string containing the XML:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_send_xml_body_from_elementtree_check_status_code_and_content_type():
    response = requests.post(
        "http://parabank.parasoft.com/parabank/services/bank/billpay?accountId=12345&amount=500",
        headers={"Content-Type": "application/xml"},
        data=create_xml_body_using_elementtree()
    )
    assert response.status_code == 200
    assert response.headers["Content-Type"] == "application/xml"</pre>

**Parsing and working with XML response bodies**  
Now that we have covered creating XML request bodies, let&#8217;s see what we can do with XML responses. By far the most powerful way to create specific assertions is to convert the XML response body into an ElementTree and then asserting on its properties.

As an example, we&#8217;re going to perform an HTTP GET call to

<http://parabank.parasoft.com/parabank/services/bank/accounts/12345>

which returns details of the account with ID 12345. If we want to assert, for example, that the root node of the XML response is named _account_, and that it has neither any attributes nor a text value, we can do this as follows:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_check_root_of_xml_response():
    response = requests.get("http://parabank.parasoft.com/parabank/services/bank/accounts/12345")
    response_body_as_xml = et.fromstring(response.content)
    xml_tree = et.ElementTree(response_body_as_xml)
    root = xml_tree.getroot()
    assert root.tag == "account"
    assert len(root.attrib) == 0
    assert root.text is None</pre>

Note that we first have to convert the XML response body to an object of type _Element_ using the _fromstring()_ method, then create an _ElementTree_ out of that using the _ElementTree()_ constructor, which takes an _Element_ as its argument.

If we&#8217;re interested in the text value of a specific subelement of the XML response, for example _customerId_ which contains the ID of the customer to whom this account belongs, we can do that by finding it in the ElementTree using the _find()_ method, then write an assertion on the _text_ property of the found element:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_check_specific_element_of_xml_response():
    response = requests.get("http://parabank.parasoft.com/parabank/services/bank/accounts/12345")
    response_body_as_xml = et.fromstring(response.content)
    xml_tree = et.ElementTree(response_body_as_xml)
    first_name = xml_tree.find("customerId")
    assert first_name.text == "12212"</pre>

It&#8217;s good to know that the _find()_ method returns the first occurrence of a specific element. If we want to return all elements that match a specific name, we need to use _findall()_ instead:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_use_xpath_for_more_sophisticated_checks():
    response = requests.get("http://parabank.parasoft.com/parabank/services/bank/customers/12212/accounts")
    response_body_as_xml = et.fromstring(response.content)
    xml_tree = et.ElementTree(response_body_as_xml)
    savings_accounts = xml_tree.findall(".//account/type[.=&#039;SAVINGS&#039;]")
    assert len(savings_accounts) &gt; 1</pre>

As you can see, next to passing element names directly, we can also use XPath expressions to perform more sophisticated selections. The expression

<pre class="wp-block-preformatted">.//account/type[.=&#039;SAVINGS&#039;]</pre>

in the example above selects all occurrences of the _type_ element (a child element of _account_) that have _SAVINGS_ as their element value.

**Using the examples for yourself**  
The code examples I have used in this blog post can be found on <a href="https://github.com/basdijkstra/ota-examples/tree/master/python-requests" target="_blank" rel="noreferrer noopener" aria-label="my GitHub page (opens in a new tab)">my GitHub page</a>. If you download the project and (given you have installed Python properly) run

<pre class="wp-block-preformatted">pip install -r requirements.txt</pre>

from the root of the python-requests project to install the required libraries, you should be able to run the tests for yourself. See you next time!