---
title: An introduction to contract testing - part 1 - meet the players
layout: post
permalink: /an-introduction-to-contract-testing-part-1-meet-the-players/
categories:
  - consumer driven contract testing
tags:
  - contract testing
  - pact
  - api
---
_In this series of articles, you'll be introduced to a (fictitious but realistic) use case for consumer-driven contract testing with [Pact](https://docs.pact.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-meet-the-players){:target="_blank"} and [Pactflow](https://pactflow.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-meet-the-players){:target="_blank"}. Over 6 articles, you'll read about:_

* _The different parties that play a role in this use case and the challenges that integration and end-to-end testing pose for them (**this article**)_
* _[How contract testing can address these challenges](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/)_
* _[How to use Pact for consumer-driven contract testing](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/)_
* _[How to make contract testing an integral part of an automated development and delivery workflow](/an-introduction-to-contract-testing-part-4-automating-the-workflow/)_
* _What the effect is of changes in the expectations and implementations of the consumer and provider parties_
* _How to invite new parties to the contract testing ecosystem and how bidirectional contracts can make this a smooth process_

_All code samples that are shown and referenced in these articles can be found [on GitHub](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"}._

In the last decade or so, software system architectures have moved from monolithic, to service-oriented, to highly distributed and often microservices-based. Where in the past a team or department was responsible for developing and delivering an entire system, responsibilities are now often distributed over different teams, working for different departments and often even for different companies.

This distributed approach to software development has some significant benefits, mainly in terms of flexibility and scalability:

* Deploying a new version of a single component, or swapping it out for a replacement that does the job better, does not require a full redeployment of the entire system
* When different teams are working on different components of the same system, development can be done in parallel, with significant gains in speed as a result
* If a single component has to handle a lot of requests, it can be scaled without also having to scale the rest of the components

Apart from these, there are many more benefits of distributed systems development. However, this approach to software development also comes with its own challenges, especially in the area of integration and end-to-end testing. To take a closer look at these issues and how they can be addressed, let's take a look at a sample application composed out of several loosely coupled components.

## The application
This article and the upcoming ones in this series, will revolve around an online store that sells and delivers sandwiches across the United States. The application is designed and developed using a microservices-based architecture. Initially, we will focus on three components that are part of this architecture.

### A Customer API (consumer)
One of the key tasks of our online store is keeping track of customer data, not just for delivering orders to the right person, but also to keep track of recurring sales, promotions and advertising means. This component consumes data exposed by an Address API, which we will read about soon, to link billing addresses to customers. One of the responsibilities of this Customer API is to expose customer data to the website and mobile app so users can see and edit their personal data.

The Customer API is developed and run, in true DevOps fashion, by team Customer, which is located in the main office of our sandwich delivery company.

## An order API (consumer)
If our store was not able to take and process orders, we would be out of business pretty soon. To keep track of orders and all associated data, we use an Order API. This API, too, is a consumer of the Address API, and is used to keep track of delivery addresses for individual orders. Order data, too, is exposed to the web frontend of our online store, as well as the mobile app that people can use to order their sandwiches.

The Order API is developed and run by team Order. Like team Customer, they are located in the main office of our sandwich delivery company.

## An address API (provider)
As mentioned, both the Customer and the Order API consume data related to billing and delivery addresses, respectively. These data are provided by an Address API, which offers the following Create-Read-Update-Delete (CRUD) operations on addresses:

`GET /address/{id}`

When an address with id `{id}` is found in the address database, this operation returns a payload that looks like this:

{% highlight json %}
{
    "id": "87256abc-f6b3-4e91-9f60-3ca3f54863d5",
    "addressType": "billing",
    "street": "Main Street",
    "number": 123,
    "city": "Nothingville",
    "zipCode": 54321,
    "state": "Tennessee",
    "country": "United States"
}
{% endhighlight %}

`POST /address`

This operation creates a new address entry in the database. It accepts a request payload that looks like this (all fields are mandatory):

{% highlight json %}
{
    "addressType": "billing",
    "street": "Main Street",
    "number": 123,
    "city": "Nothingville",
    "zipCode": 54321,
    "state": "Tennessee",
    "country": "United States"
}
{% endhighlight %}

`PUT /address/{id}`

This operation updates an existing address in the database. The accepted request payload is the same as the one for the `POST` operation.

`DELETE /address/{id}`

Finally, the API also offers an operation to delete an existing address from the database.

## The organization
The Address API is developed and run by team Address. Unlike the Customer and Order teams mentioned earlier, they are located in a branch office on the other side of the country.

Here's a graphical representation of the relations between the components described above:

![contract testing players](/images/blog/contract_testing_players.png "The players in our contract testing case and their relationships")

## The testing process
All teams responsible for ultimately delivering the online sandwiches store, including our Customer, Order and Address teams, do their due diligence when it comes to testing the components they develop. They write unit tests and functional acceptance tests, do static code analysis and linting, and even perform security and performance testing, all integrated into their respective delivery pipelines.

Integration and end-to-end testing, however, is a different matter. The teams suffer from various issues when they try to write and run these types of tests, including:

* To perform end-to-end tests, a complex test environment consisting of all components involved needs to be provisioned every time these tests need to be run
* Different components being developed in different teams, each with their own delivery heartbeat and feature backlog, means that teams often have to wait for other teams to finish their work before integration testing can be performed
* It is not always clear who is responsible for the integration testing, since the scope of these tests crosses individual team boundaries
* Since the Address team is located on the other side of the country, there's a communication barrier as it is not possible to simply walk over to their desk. A lot of communication is done via email, Slack and Jira, but a lot of information goes lost in translation

These issues lead to low integration and end-to-end testing coverage, and the company has suffered the consequences in the past as integration issues have made their way into the production environment. Therefore, the company needs a way to do better...

We'll take a closer look at how the teams address these challenges in the second article in this series.
