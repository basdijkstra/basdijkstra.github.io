---
title: Security testing your APIs - Broken Function Level Authorization
layout: post
permalink: /security-testing-your-apis-broken-function-level-authorization/
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

1. _[Broken Object Level Authorization](/security-testing-your-apis-broken-object-level-authorization/)_
2. _Broken Authentication_
3. _Broken Object Property Level Authorization_
4. _Unrestricted Resource Consumption_
5. _Broken Function Level Authorization (**this post**)_
6. _[Unrestricted Access to Sensitive Business Flows](/security-testing-your-apis-unrestricted-access-to-sensitive-business-flows/)_
7. _Server Side Request Forgery_
8. _Security Misconfiguration_
9. _Improper Inventory Management_
10. _Unsafe Consumption of APIs_

### What is Broken Function Level Authorization?
An API is suffering from [Broken Function Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa5-broken-function-level-authorization/){:target="_blank"}, or BFLA in short, when it is possible for a user to perform operations on resources that they, by design, should not have been able to perform. Where [Broken Object Level Authorization](/security-testing-your-apis-broken-object-level-authorization/) is about _access to_ data, BFLA is about modifying or deleting data.

As an example, consider the scenario where you're a customer of an online bank, and you can retrieve the details for any of your bank accounts through some secure part of the bank's website. It is likely that, when you want to see your account details, the website will call an API to retrieve the details for your accounts from the backend system using a call such as `GET /accounts/123`, where `123` is your unique account ID. This API request will likely also contain a header containing a token, a cookie or whatever else uniquely identifies and authenticates you.

One example of a BFLA violation would be when it is possible for the same user, using the same authentication details, to update the details for that account. This could be done, for example, by sending an HTTP PUT or PATCH to `/accounts/123` with an updated account balance (free money!).

### Testing for BFLA violations
Let's have a look at the [API for the ParaBank demo application](https://parabank.parasoft.com/parabank/api-docs/index.html){:target="_blank"}.

Assuming we have supplied valid credentials, and an account with ID 12345 is associated with our user with customer ID 12212, we can retrieve the details for account 12345 using an HTTP GET to `/accounts/12345`. This returns the account details, which look something like this:

{% highlight json %}
{
    "id": 12345,
    "customerId": 12212,
    "type": "CHECKING",
    "balance": -2300.00
}
{% endhighlight %}

This is as expected, with the exception, maybe, of the rather disappointing balance. Speaking of that balance, let's see if we can do something about that. Of course, we _could_ see if we can get a pay raise to work on our balance, or maybe even find another job, but what if there was an easier way to address our financial issues?

What if we could simply update the balance with an appropriately constructed PUT or PATCH call? The API documentation does not contain any information about such an operation, but that shouldn't stop us from trying it out anyway. On the contrary, the fact that this operation is not in the documentation should be all the more reason to try it out and see what happens.

First, let's try an HTTP PATCH to `/accounts/12345` with the following request body:

{% highlight json %}
{
    "balance": 1500.00
}
{% endhighlight %}

Unfortunately, this returns a response with HTTP 405 (Method Not Allowed). No luck. Next, let's try an HTTP PUT with this request body:

{% highlight json %}
{
    "id": 12345,
    "customerId": 12212,
    "type": "CHECKING",
    "balance": 1500.00
}
{% endhighlight %}

Alas, the same result: this operation is also not allowed. Neither is an HTTP POST.

Finally, we could try and delete the account altogether using an HTTP DELETE on `/accounts/12345`. Getting rid of the account would be another way of getting rid of our financial troubles. Unfortunately for us, this attempt, too, results in an HTTP 405. It seems like we'll have to go talk to our boss about that raise after all...

### Writing tests to check for BFLA violations
As you have seen in the examples above, starting to test for potential BFLA violations is pretty straightforward. You could even use tools to help you do this on every build. Here is an example of what that might look like for our example, using [RestAssured.Net](https://github.com/basdijkstra/rest-assured-net){:target="_blank"}:

{% highlight csharp %}
[TestCaseSource("ActionsOnAccounts")]
public void CheckAccountsForBFLAViolations(HttpMethod httpMethod, HttpStatusCode httpStatusCode)
{
    Given()
    .When()
        .Invoke($"http://localhost:8080/parabank/services/bank/accounts/12345", httpMethod)
    .Then()
        .StatusCode(httpStatusCode);
}

private static IEnumerable<TestCaseData> ActionsOnAccounts()
{
    yield return new TestCaseData(HttpMethod.Get, HttpStatusCode.OK).
        SetName("HTTP GET is allowed");
    yield return new TestCaseData(HttpMethod.Post, HttpStatusCode.MethodNotAllowed).
        SetName("HTTP POST is not allowed");
    yield return new TestCaseData(HttpMethod.Put, HttpStatusCode.MethodNotAllowed).
        SetName("HTTP PUT is not allowed");
    yield return new TestCaseData(HttpMethod.Patch, HttpStatusCode.MethodNotAllowed).
        SetName("HTTP PATCH is not allowed");
    yield return new TestCaseData(HttpMethod.Delete, HttpStatusCode.MethodNotAllowed).
        SetName("HTTP DELETE is not allowed");
    yield return new TestCaseData(HttpMethod.Head, HttpStatusCode.OK).
        SetName("HTTP HEAD is allowed");
    yield return new TestCaseData(HttpMethod.Options, HttpStatusCode.OK).
        SetName("HTTP OPTIONS is allowed");
}
{% endhighlight %}

This example test iterates over a list of HTTP methods, invokes them to send a request to `/accounts/12345` and checks the corresponding response status code. While this is still by all means a very shallow test (you should never blindly trust response status codes as a tester), it is a good starting point for building your own, more extensive tests to run on every build.

### How to mitigate the risks of a BFLA violation?
BFLA is on the OWASP API Security Top 10 for a reason: there is a significant amount of potential damage. Malevolent people could easily exploit a BFLA vulnerability and modify or delete sensitive data if an API is vulnerable to BFLA.

Here are some countermeasures that you can take to minimize the risk of a BFLA vulnerability:

* Test! As you can see, starting to test for BFLA violations does not require a lot of in-depth knowledge, just a healthy dose of creativity
* When designing or developing an API, deny all access to a resource by default, and only explicitly grant access to specific methods based on roles that should have access.

### More examples
More examples of BFLA violations can be found on the OWASP API Security Top 10 page dedicated to [Broken Function Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa5-broken-function-level-authorization/){:target="_blank"}.

The [API Security Fundamentals](https://www.apisecuniversity.com/courses/api-security-fundamentals){:target="_blank"} and [OWASP API Security Top 10 and Beyond](https://www.apisecuniversity.com/courses/owasp-api-security-top-10-and-beyond){:target="_blank"} courses at API Sec University contain even more examples.