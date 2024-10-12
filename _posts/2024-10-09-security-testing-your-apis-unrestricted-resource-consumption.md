---
title: Security testing your APIs - Unrestricted Resource Consumption
layout: post
permalink: /security-testing-your-apis-unrestricted-resource-consumption/
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
4. _Unrestricted Resource Consumption (**this post**)_
5. _[Broken Function Level Authorization](/security-testing-your-apis-broken-function-level-authorization/)_
6. _[Unrestricted Access to Sensitive Business Flows](/security-testing-your-apis-unrestricted-access-to-sensitive-business-flows/)_
7. _Server Side Request Forgery_
8. _Security Misconfiguration_
9. _Improper Inventory Management_
10. _Unsafe Consumption of APIs_

### What is Unrestricted Resource Consumption?
An API is said to be suffering from [Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/){:target="_blank"} if the API or endpoint does not pose any limits to the number of client requests made to it, or to access to the resources that are exposed through it.

Being vulnerable to Unrestricted Resource Consumption might lead to a number of problems, including Denial of Service-attacks and skyrocketing costs for using paid-for third-party services, but it also allows for people with malicious intent to easily search for and scrape data from your API endpoints.

### Testing for Unrestricted Resource Consumption violations

As an example, have a(nother) look at the example where we [tested for Broken Object Level Authorization](/security-testing-your-apis-broken-object-level-authorization/) in a previous article in this series. There, we scanned all account numbers between 10000 and 20000 to see if we could get access to data that we weren't supposed to see (spoiler alert: we did).

What made it possible and, in all honesty, even a breeze to do so, was the fact that the API endpoint allowed us to make these 10000 calls in quick succession, something that can be easily done using a script or even using tools like Postman and a custom data source.

Had the endpoint implemented a form of [rate limiting](https://www.cloudflare.com/learning/bots/what-is-rate-limiting/){:target="_blank"} that restricted the number of calls that could be made from a certain endpoint in a certain timeframe, it would have become significantly harder (though not impossible) to look for data we weren't supposed to access in this way.

After reaching the threshold set by the rate limiting, we would for example have received an [HTTP 429](https://http.cat/status/429){:target="_blank"} telling us we have been making too many calls within a certain timeframe.

### Mitigating the risks of a violation of Unrestricted Resource Consumption

Rate limiting is one of the most commonly countermeasures to Unrestricted Resource Consumption vulnerabilities. While it is certainly effective, it is not perfect, though:

* First, rate limiting is essentially a slider: set it too loosely and it is not effective, set it too tightly and it might negatively affect benign users of your APIs, too. It takes careful consideration and likely a bit of experimentation to find the right rate limiting settings for your situation.
* Second, in certain cases, it's not the number of calls that are made that exposes the vulnerability, but rather the nature of the individual calls. For example, if you can perform bulk operations with just a single call (something that is fairly common in GraphQL endpoints, for example), you can still do a lot of damage with only a single API call.

This is true for many API security measures in general: they help reduce the likelihood of an API becoming vulnerable to security issues, but in isolation, they do not guarantee that an API is secure.

This idea even has its own name: the [Swiss cheese model](https://en.wikipedia.org/wiki/Swiss_cheese_model){:target="_blank"} of security measures. A picture paints a thousand words here:

![swiss_cheese](/images/blog/swiss_cheese_model.png "A graphical representation of the Swiss cheese model")

In other words, by all means take your security countermeasures to reduce the risk of security issues, but don't rely on any single countermeasure in isolation to protect you from security incidents.

### More examples
More examples of Unrestricted Resource Consumption violations can be found on the OWASP API Security Top 10 page dedicated to [Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/){:target="_blank"}.

The [API Security Fundamentals](https://www.apisecuniversity.com/courses/api-security-fundamentals){:target="_blank"} and [OWASP API Security Top 10 and Beyond](https://www.apisecuniversity.com/courses/owasp-api-security-top-10-and-beyond){:target="_blank"} courses at API Sec University contain even more examples.