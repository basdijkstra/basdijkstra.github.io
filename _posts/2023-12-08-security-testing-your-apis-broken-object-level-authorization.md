---
title: Security testing your APIs - Broken Object Level Authorization
layout: post
permalink: /security-testing-your-apis-broken-object-level-authorization/
categories:
  - API testing
tags:
  - security testing
  - OWASP API top 10
  - APIs
---
_In this blog post series, I am going to explore the vulnerabilities in the [OWASP API Security Top 10](https://owasp.org/API-Security/editions/2023/en/0x00-header/){:target="_blank"}. For each entry, I'll show you how to perform experiments on APIs to test for the vulnerability, and I'll discuss my observations._

_I'll use different APIs as test subjects in these blog posts. All APIs used are demonstration APIs, i.e., they are not used in real-life, public applications. Any vulnerabilities we discover in these APIs are therefore harmless, if not put in there on purpose._

_Here are the entries:_

1. _Broken Object Level Authorization (**this post**)_
2. _Broken Authentication_
3. _Broken Object Property Level Authorization_
4. _Unrestricted Resource Consumption_
5. _[Broken Function Level Authorization](/security-testing-your-apis-broken-function-level-authorization/)_
6. _Unrestricted Access to Sensitive Business Flows_
7. _Server Side Request Forgery_
8. _Security Misconfiguration_
9. _Improper Inventory Management_
10. _Unsafe Consumption of APIs_

### What is Broken Object Level Authorization?
An API is suffering from [Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/){:target="_blank"}, or BOLA in short, when it is possible for a user to access resources that they, by design, should not have access to.

As an example, consider the scenario where you're a customer of an online bank, and you can retrieve a list of your bank accounts through some secure part of the bank's website. It is likely that, when you want to see your accounts overview, the website will call an API to retrieve the details for your accounts from the backend system using a call such as `GET /customers/123/accounts`, where `123` is your unique customer ID. This API request will likely also contain a header containing a token, a cookie or whatever else uniquely identifies and authenticates you.

One example of a BOLA violation would be when it is possible for the same user, using the same authentication details, to retrieve account details for other customers.

### Testing for BOLA violations
Let's have a look at the [API for the ParaBank demo application](https://parabank.parasoft.com/parabank/api-docs/index.html){:target="_blank"}. Let's assume I have retrieved a valid authentication token for the customer with ID `12212`. This is a big assumption, as this particular API doesn't even use authentication tokens, but work with me here.

When authenticated, we can retrieve the list of accounts for this customer using an HTTP GET to `/customers/12212/accounts`. This returns a list of account details that might look something like this:

{% highlight json %}
[
    {
        "id": 12345,
        "customerId": 12212,
        "type": "CHECKING",
        "balance": -2300.00
    },
    {
        "id": 12456,
        "customerId": 12212,
        "type": "CHECKING",
        "balance": 10.45
    },
    {
        "id": 12567,
        "customerId": 12212,
        "type": "CHECKING",
        "balance": 100.00
    }
]
{% endhighlight %}

Apart from their balance on account `12345`, all is well here. But that customer ID looks pretty predictable, almost like it's a value that is simply incremented by 1 for each new customer.

What would happen if we try and retrieve the list of accounts for other customer IDs that are roughly in the same range, for example all values between 10000 and 20000?

As a first try, when we increment the customer ID by 1 and perform an HTTP GET to `/customers/12213/accounts`, we receive a response with an HTTP 400 status code, containing a message

`Could not find customer #12213`

Instead of telling me that I am not authorized to see the details for customer `12213` (remember, we have a valid authentication token for customer `12212`), it tells me there is no data for customer `12213`. This is a good thing, because it prevents leaking of potentially vulnerable information, i.e., the fact that a user with ID `12213` exists.

The choice of returning an HTTP 400 is a little curious, though, I would have expected this to be a 404: there's nothing wrong with the request per se.

But what would happen if we stumble upon a customer ID that _does_ exist? In this API, there is another customer with ID `12323`. I happen to know this, but even if I didn't, it would be very easy to find out. We could simply create a script that performs the HTTP GET and uses all values between 10000 and 20000 as a customer ID.

When we perform an HTTP GET to `/customers/12323/accounts` with our token for customer `12212`, this is what the response looks like:

{% highlight json %}
[
    {
        "id": 13455,
        "customerId": 12323,
        "type": "CHECKING",
        "balance": 2014.76
    }
]
{% endhighlight %}

This is a clear example of a BOLA vulnerability: we are able to see the details for accounts associated with customer `12323`, even if we are authenticated as customer `12212`. And it didn't take us very long to find it, either!

This doesn't bode very well for the rest of the API: if we are able to see data we shouldn't be able to see, it's not unlikely we can also do even more damaging things, such as transferring money that isn't ours into our account.

And that's exactly what is possible. If you want to know how, just read Edward Lichtner's [blog post on exploring and hacking the ParaBank API](https://zerodayhacker.com/parabank-walkthrough/){:target="_blank"}.

### What to do now?
There is a reason BOLA is the number 1 on the OWASP API Security Top 10: the potential damage is enormous. People with malicious intent could easily exploit a BOLA vulnerability and get access to all kinds of sensitive data.

Here are some countermeasures that you can take to minimize the risk of a BOLA vulnerability:

* Test! As you can see, testing for BOLA violations does not require a lot of in-depth knowledge, just an active user session and a little creativity
* Implement proper role-based / user-based authorization mechanisms, and then use those to check if a user is authorized to access a resource every time they try and access it

OWASP also recommends the use of resource IDs that are harder to guess, such as GUIDs, as an additional layer of safety.

Yet another layer of safety comes in the form of implementing rate limiting, to prevent people from running scripts to scan for data, or at least make it harder for them to do so. More on rate limiting in another blog post.

Like with many other security measures, no single measure will guarantee that you're safe from security vulnerabilities, but adding several greatly reduces the risk of such a vulnerability. This approach is called the [Swiss cheese model](https://en.wikipedia.org/wiki/Swiss_cheese_model){:target="_blank"}.

### More examples
More examples of BOLA violations can be found on the OWASP API Security Top 10 page dedicated to [Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/){:target="_blank"}.

The [API Security Fundamentals](https://www.apisecuniversity.com/courses/api-security-fundamentals){:target="_blank"} and [OWASP API Security Top 10 and Beyond](https://www.apisecuniversity.com/courses/owasp-api-security-top-10-and-beyond){:target="_blank"} at API Sec University contain even more examples.