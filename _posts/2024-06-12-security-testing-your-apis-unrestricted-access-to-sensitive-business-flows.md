---
title: Security testing your APIs - Unrestricted Access to Sensitive Business Flows
layout: post
permalink: /security-testing-your-apis-unrestricted-access-to-sensitive-business-flows/
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
5. _[Broken Function Level Authorization](/security-testing-your-apis-broken-function-level-authorization/)_
6. _Unrestricted Access to Sensitive Business Flows (**this post**)_
7. _Server Side Request Forgery_
8. _Security Misconfiguration_
9. _Improper Inventory Management_
10. _Unsafe Consumption of APIs_

### What is Unrestricted Access to Sensitive Business Flows?
An API is said to be suffering from [Unrestricted Access to Sensitive Business Flows](https://owasp.org/API-Security/editions/2023/en/0xa6-unrestricted-access-to-sensitive-business-flows/){:target="_blank"}, or UASBF in short, if a user with malicious intent can use the API of a system to perform undesirable actions on business processes implemented by the system that exposes the API. The goal of a user exploiting this vulnerability could be either to flood or corrupt the system, or to obtain undesirable or illegal gains from it.

As an example, consider a scenario where a limited edition item goes on sale at a certain point in time. This could be concert tickets, some kind of collectible article, tickets for a sports event, it doesn't really matter.

If your system or platform selling these items exposes the logic to purchase them via an API, it would be a good idea to inspect that someone looking to make a profit from these items isn't able to do so, because of the (potential) damage to your reputation as a trustworthy and fair provider of products or services.

### Testing for UASBF violations
Let's look at an example for a hypothetical game shop that mostly sells regular, everyday games, such as Mario Kart. An order is created in the backend through an HTTP POST to the `/order` endpoint, with a payload that looks like this:

{% highlight json %}
{
    "customerId": 12345,
    "articleId": 432,
    "articleDescription": "Mario Kart",
    "quantity": 1
}
{% endhighlight %}

For these regular items, the game shop does not impose a limit to the number of copies a single customer can buy. If a customer wants 5 copies of the game, the request payload would be

{% highlight json %}
{
    "customerId": 12345,
    "articleId": 432,
    "articleDescription": "Mario Kart",
    "quantity": 5
}
{% endhighlight %}

At certain times, though, our game shop gets their hands on a limited supply of collector items, and to keep as many customers happy as they can, and decrease the chance of scalping, they limit the number of copies that an individual customer can order to 1:

{% highlight json %}
{
    "customerId": 12345,
    "articleId": 987,
    "articleDescription": "Gamma Attack",
    "quantity": 1
}
{% endhighlight %}

Trying to order multiple copies of Gamma Attack, like we did with Mario Kart in the example results in an error message:

{% highlight json %}
{
    "error": "Only 1 copy of Gamma Attack per person!"
}
{% endhighlight %}

So far, so good. But maybe there are other ways to order multiple copies of this limited edition, 1-copy-per-person-only item?

It turns out, there are! While it's impossible to order multiple copies of Gamma Attack _in a single order_, there's no limit on the number of orders for Gamma Attack that any single customer can place. This enables them to scalp all the copies simply by placing many order, each for a single copy, as soon as Gamma Attack goes on sale.

### How can we prevent UASBF violations?
The most important and most effective thing you can do to try and prevent your API from becoming vulnerable to UASBF is follow this four-step process before you start implementing and releasing your API:

1. Identify what your sensitive business flows are and what the impact of people abusing these business flows would be - in the example here, that would be the ordering process for limited edition items in our game shop
2. Identify the different ways in which people might abuse those sensitive business flows - in the example, that would be by ordering multiple copies of Gamma Attack in consecutive orders in quick succession
3. Implement countermeasures that help prevent the abuse of these sensitive business flows - more about these in a moment
4. Perform tests to gather information telling you whether the risks concerning your sensitive business flows have been adequately mitigated

Ideally, steps 1 to 3 are performed _before_ you start implementing the API and the business logic underneath. I recommend running discovery sessions with developers, testers and product persons to minimize the risk of sensitive business flows and ways to get unrestricted access to them remaining undiscovered. 

### Mitigating the risks of a UASBF violation
UASBF is number 6 on the 2023 OWASP API Security Top 10 for good reason: when people with malevolent intent are frequently able to abuse sensitive business flows, thereby for example scalping items with limited availability or flooding your system with orders or reservations, there is a significant risk of damage to the image of your business and the amount of trust that people place in your ability to operate business in a fair way.

Here are some technical countermeasures that you can take to minimize the risk of a UASBF vulnerability:

* Device fingerprinting - this allows you to deny service to for example headless browsers, which are often used by users with malicious intent
* Human detection - using captchas or more advanced (biometric) solutions detecting whether it's a human or a bot performing an action
* Detection of non-human usage patterns - for example, if the 'add to cart' and 'checkout' actions are performed within milliseconds, there's a good chance there's a bot at work

### More examples
More examples of UASBF violations can be found on the OWASP API Security Top 10 page dedicated to [Unrestricted Access to Sensitive Business Flows](https://owasp.org/API-Security/editions/2023/en/0xa6-unrestricted-access-to-sensitive-business-flows/){:target="_blank"}.

The [API Security Fundamentals](https://www.apisecuniversity.com/courses/api-security-fundamentals){:target="_blank"} and [OWASP API Security Top 10 and Beyond](https://www.apisecuniversity.com/courses/owasp-api-security-top-10-and-beyond){:target="_blank"} courses at API Sec University contain even more examples.