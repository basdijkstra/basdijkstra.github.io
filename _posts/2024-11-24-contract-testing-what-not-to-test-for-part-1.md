---
title: Contract testing - what (not) to test for - part 1
layout: post
permalink: /contract-testing-what-not-to-test-for-part-1/
categories:
  - Contract testing
tags:
  - consumer-driven contract testing
  - testing integration or testing implementation 
  - Pact
---
Recently, I started working with a new client who have been working on their contract testing implementation for a while and figured out they could use some outside help. I've paid them a visit recently, and to make the most out of our time together (there was some travel involved and I could really only spend one day on site with them this time), they sent me a _long_ list of questions that the different development teams have gathered over the past few months.

These questions covered several aspects of contract testing, but a few of them inspired me to write this, specifically because I've seen many teams struggle with very similar questions. I'm currently working with three different companies, helping them with their contract testing, and I've seen questions of this kind come up with all three of them.

In fact, to be specific, it's not the questions themselves that made me come up with this post, but rather something that underpinned them, and that's the issue of

_"Where does contract testing end and testing the implementation of a provider service begin?"_

As I try and make an effort to mention, both in the [talks](/talks/) I've done on contract testing and the [contract testing training courses](https://ontestautomation.teachable.com/p/practical-contract-testing-with-pact){:target="_blank"} and [workshops](/training/) I've facilitated, contract testing is a technique that is complementary to functional / acceptance testing of (provider) services, not a replacement for it. If contract testing replaces anything, it's part of your integration and end-to-end tests, not tests for an individual service.

To phrase this in a different way, where functional testing of an individual service focuses on finding an answer to

_"Does this service meet the expectations around its behaviour set by the team that develops the service?"_

contract testing, especially that of the [consumer-driven flavour](/approaches-to-contract-testing/), focuses on finding an answer to the question of

_"Does a given provider meet the expectations around its behaviour set by its consumers?"_

The boundary between these two questions, and the line where one type of testing ends and the other begins, is not always crystal clear, though. Let's have a look at an example to explore what I think should, and what should not, be part of a contract test.

#### The context discussed in this blog post
In this blog post, I'll focus on HTTP-based integrations alone. In a follow-up blog post, I'll explore this issue for messaging-based systems. While there are many overlaps between the two types of integrations when it comes to the question of 'what (not) to put in a contract test?', there are some things that are unique to messaging-based systems that warrant some dedicated attention.

The client I spoke about at the start of this blog post pretty much exclusively uses messaging-based integrations, by the way, so this blog post might not be especially relevant to their situation, but again, the problem I'm discussing here is valid for any type of integration.

Please also note that this blog post assumes a consumer-driven contract testing approach. In the follow-up blog post I just mentioned, I also want to take a look at the question from a bidirectional contract testing perspective.

To address the question we're working on here, let's consider an HTTP-based integration between a Customer service as the consumer and an Address service as the provider as an example. First, let's have a look at the operation where the Customer service retrieves address data from the Address service using an HTTP GET call to an endpoint that looks like this:

`/address/{id}`

where `{id}` uniquely identifies a specific address resource in the provider database. Let's also assume that the service uses UUIDs as resource ID values, and that an example of the JSON representation of an address as it is returned by the provider looks like this:

{% highlight json %}
{
    "street": "Main Street",
    "number": 123,
    "zipCode": "90210",
    "city": "Beverly Hills",
    "country": "United States",
    "updated": "2024-11-22T12:34:56+01:00"
}
{% endhighlight %}

Finally, please consider that our consumer currently only uses addresses in the United States, and that it does not actually use the city name. In the US, the relation between zip code and city is 1-on-1, so sending both the `city` and the `zipCode` values is redundant from the Customer consumer service perspective.

#### Scenarios to verify
We can identify different scenarios here, including:

* A scenario where the resource ID is a valid UUID and the provider database contains corresponding address data - the HTTP 200 scenario
* A scenario where the resource ID is a valid UUID but the provider database does not contain corresponding address data - the HTTP 404 scenario
* A scenario where the resource ID is not a valid UUID - the HTTP 400 scenario

There might be other scenarios as well, for example based on whether the request contains valid credentials for authentication, but let's stick with these three for now.

Please note that it is important to think about potential scenarios when you start writing contract tests, as error scenarios (those with a response status code in the 400 range) as just as important to cover as the successful ones (those with a response in the 200 range).

#### The 'happy path' scenario and expectations around response body shape
Let's start with the HTTP 200 scenario. What we do want to specify in the contract is those properties we absolutely need as a consumer to process the response, but no more than that. Why not? Because the more you ask for, and the more specific your demands are as a consumer, the higher the chance that the provider cannot meet your needs, which would lead to potential false positives when the contract tests are run. Plus, when there are multiple consumer relying on the same provider, the more you ask for, the higher the chance of a potential conflict of interest between individual consumers, too.

In other words, ask for what you cannot do without, but nothing more. Or: 'ask for as little as you can get away with'.

So, for the HTTP 200 scenario, we might specify in our consumer contract that we expect that the provider returns:

* an HTTP 200 status code
* a response header called `Content-Type` with a value equal to `application/json` to tell the consumer how to parse the response body
* a body that is formatted in a shape that can be parsed and processed

What we do not want to specify in our contract is what that response body would exactly look like for a specific address ID.

So, what we do want to state in our contract is that

* The `street` field should have a string value, and that
* The `number` field should have an integer value

We do NOT want to specify that, if we retrieve an address with a specific ID, that

* The `street` field should have the exact value `Main Street`, and that
* The `number` field should have the exact value `123`

Why not? Because doing so would no longer mean we're testing the integration between consumer and provider ('can I parse a successfully returned address representation?'), it would mean we're testing provider implementation ('does the provider return the expected data when I ask them for a specific address?').

The latter is not the purpose of a contract test. Sure, you could use contract testing and Pact to do that, but it would quickly lead to very brittle tests because of the high coupling between consumer and provider test code.

But what about the `country` field in the response? As I mentioned earlier, our customer currently only expects United States-based addresses. This means that for now, we could be more strict with our expectations around the `country` field, and write in our contract that the value of this field always has to be equal to `United States`.

However, there are also arguments to be made for _not_ doing so, and sticking to verifying that the value of the `country` field is a string:

* (_important_) while _this_ consumer only supports US-based addresses, the provider might support addresses for other countries, too, and could therefore return another value for the `country` field. Expecting the provider to return a specific value means that technically, we're sort of testing provider implementation logic here, and not just consumer-provider integration
* (_less important_) in the future, this consumer might support addresses in other countries, too, leading to rework on the consumer tests

The first reason is an example of the boundaries between testing consumer-provider integration and testing provider implementation is not always crystal clear. Again, it is often a good idea to ask for as little as you can get away with, not for as much as you can.

Next, let's look at the `updated` field. Here, as a consumer, we definitely do want to be a little more strict than just checking the element data type (string). While it doesn't make sense to verify the exact date and time of the last modification of a certain address, we do want to make sure that we can parse the element value. In other words, the date and time should be returned in a format that makes sense for us as a consumer.

One way to do that is by using regular expressions, and contract testing tools such as Pact support expressing expectations around the shape of a certain field value to match a predefined regular expression. Perfect for this kind of situation.

We can have the same discussion for the `zipCode` field: we can require that our zip code is a five-digit number (even though the value is returned as a string) by requiring the value returned to match a regular expression like `\d{5}`, but we are toeing the boundary of contract testing and testing implementation here, and there is a risk of false positives.

If you choose to be more specific and require a specific formatting for the zip code, you probably want to make sure that the [provider state](https://docs.pact.io/getting_started/provider_states){:target="_blank"} you're passing with this interaction specifically requires the provider to set up a US-based address.

The downside of this is that you might end up with a large number of very specific provider states, which complicates contract testing adoption on the provider side as they have to cater for more provider states in their verification tests.

And it is very important as a consumer to keep assuming that there will be other consumers relying on the same provider, too. I'm starting to sound like a broken record here, but: only ask for what you can't do without.

Finally, as we stated before, the Customer service does not need the value of the `city` field as it completely relies on the value of the `zipCode` field when processing the address data, so it's a good idea to leave it out of the consumer expectations. One final time: only ask for what you can't do without.

#### Error paths
Now that we have covered what the contract should say in case of the HTTP 200 scenario, we can repeat this exercise for the HTTP 404 and HTTP 400 scenarios. In these scenario, it is likely that our Customer service does not expect the Address provider to return any data, so the only expectation as a consumer is that the provider returns the correct HTTP status code, which is 404 and 400, respectively.

For operations other than HTTP GET, the process is much the same. Identify the appropriate scenarios based on HTTP status codes. Then, for every scenario, think about 'what do I absolutely need from the provider in order to be able to process the response?' and be careful of that fine line between testing consumer-provider integration and testing provider implementation.

#### Wrap up and 'to be continued...'
In this post, I have tried to shed some light on the boundary where consumer-provider integration testing stops and provider implementation testing starts for HTTP-based integrations. As we have seen, this boundary is not always clear, and it remains something to carefully monitor when writing and running contract-based integration tests.

After all, what we want to avoid is a set of very brittle contract tests that fall over and require analysis, discussions and rework every time some implementation detail changes on the provider side. That's what provider-side unit or system tests are for.

In a follow-up post, I'll explore the question we addressed here for non-HTTP-based integrations, as these are subtly different from HTTP-based integrations when it comes to their contract testing approach. As promised, I will also look at the question discussed in this blog post from a bidirectional contract testing point of view.