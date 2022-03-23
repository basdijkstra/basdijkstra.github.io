---
title: An introduction to contract testing - part 4 - automating the workflow
layout: post
permalink: /an-introduction-to-contract-testing-part-4-automating-the-workflow/
categories:
  - consumer driven contract testing
tags:
  - contract testing
  - pact
  - api
---
_In this series of articles, you'll be introduced to a (fictitious but realistic) use case for consumer-driven contract testing with [Pact](https://docs.pact.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"} and [Pactflow](https://pactflow.io?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}. Over 6 articles, you'll read about:_

* _[The different parties that play a role in this use case and the challenges that integration and end-to-end testing pose for them](/an-introduction-to-contract-testing-part-1-meet-the-players/)_
* _[How contract testing can address these challenges](/an-introduction-to-contract-testing-part-2-introducing-contract-testing/)_
* _[How to use Pact for consumer-driven contract testing](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/)_
* _How to make contract testing an integral part of an automated development and delivery workflow (**this article**)_
* _[What the effect is of changes in the expectations and implementations of the consumer and provider parties](/an-introduction-to-contract-testing-part-5-adapting-to-changes/)_
* _[How to invite new parties to the contract testing ecosystem and how bidirectional contracts can make this a smooth process](/an-introduction-to-contract-testing-part-6-bi-directional-contracts/)_

_All code samples that are shown and referenced in these articles can be found [on GitHub](https://github.com/basdijkstra/introduction-to-contract-testing){:target="_blank"}._

In [article three in this series](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/), we have seen how to write consumer-driven contract tests for our Customer and Order consumers and Address providers as a means to detect potential integration issues. The process of generating and distributing contracts on the provider side, and retrieving and verifying them on the provider side, still included quite a few manual steps. Even worse, we did not have a means of closing the feedback loop yet, as there was no way to let all parties involved know about the results of the provider-side contract verification.

In this article, you'll see how to automate the different steps in the consumer-driven contract testing flow, allowing you to add these tests to your automated delivery pipelines.

### Step 1: Running consumer tests from the command line
The first (small but significant) step is adding the ability to run tests at the consumer side from the command line, as that is the way Continuous Integration orchestrators, such as Jenkins, GitLab, or Azure DevOps, will start the tests. In the examples used for this blog series, we'll do that by running our tests through Maven using

`mvn clean test`

as a command line command. This yields the following output (example for the customer-consumer project):

```
...
[INFO] Results:
[INFO]
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  19.971 s
[INFO] Finished at: 2021-07-03T08:02:07+02:00
[INFO] ------------------------------------------------------------------------
```

When running our consumer-side tests, the latest version of the contract as created [in the previous article](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/) will be written to the `/target/pacts` folder.

_Note: while this should have been a straightforward step, I had some troubles getting it to work in the example project. A big shoutout to the wonderful and very active Pact community on [Slack](https://slack.pact.io/){:target="_blank"} and especially to [Matt](https://www.linkedin.com/in/digitalmatt/){:target="_blank"} and [Ron](https://www.linkedin.com/in/ronald-holshausen/){:target="_blank"} for helping out and getting this all to work._

### Step 2: Publishing the generated contracts
In the [previous article](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/), distributing contracts between consumer and provider was done by physically copying the generated contracts and placing them in the right folder in the provider codebase. Needless to say, that's a way of working that is pretty error-prone and doesn't really scale at all. We need a better mechanism!

Enter the [Pact Broker](https://docs.pact.io/getting_started/sharing_pacts/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}. The Pact Broker is essentially a repository of contracts that enables:

* Consumers to upload their contracts after generating them
* Providers to pull those contracts for verification
* Providers to upload verification results

The Pact Broker allows for versioning of contracts, too, meaning that it can store different versions of contracts for a single consumer-provider pair. Documentation for the Pact Broker can be found [here](https://docs.pact.io/pact_broker/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}.

Before we see how we can automatically publish our consumer contract to the Pact Broker, it's good to know that there are two different options for hosting the Broker:

* Self-hosted: the Broker is available [as a Docker image](https://docs.pact.io/pact_broker/docker_images/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}
* As a service: the team behind Pact also offer [Pactflow](https://pactflow.io/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}, a cloud-based Pact Broker that comes with a number of additional features compared to the Dockerized version

_Full disclosure: Pactflow is a commercial offering (with a free plan). However, I have zero financial interest in recommending Pactflow as a solution. The reason I'm showing Pactflow in some of the images in this post is purely because it was easier to set up for me locally. Everything you're seeing in this post can be done with the free, Dockerized Pact Broker as well._

To publish the generated contracts to the Pactflow Pact Broker, I'm using the [Pact Maven plugin](https://search.maven.org/search?q=a:pact-jvm-provider-maven){:target="_blank"} with the following configuration:

{% highlight xml %}
<plugin>
    <groupId>au.com.dius</groupId>
    <artifactId>pact-jvm-provider-maven</artifactId>
    <version>4.0.10</version>
    <configuration>
        <pactBrokerUrl>https://ota.pact.dius.com.au</pactBrokerUrl>
        <pactBrokerToken>YOUR_PACT_BROKER_TOKEN_GOES_HERE</pactBrokerToken>
        <pactBrokerAuthenticationScheme>Bearer</pactBrokerAuthenticationScheme>
    </configuration>
</plugin>
{% endhighlight %}

_Note: the 'provider' in the plugin name is not a typo. This plugin was originally developed to be used on the provider end, and later extended to also allow it to be used on the consumer side. The Pact team preferred this over developing and maintaining two separate plugins._

After adding this plugin to our project, we can publish the contracts generated in the previous step using

`mvn pact:publish`

After doing this for both our consumers, you can see two new contracts having been uploaded to the Broker. Both of them are as of yet unverified (for obvious reasons):

![unverified contracts](/images/blog/pact_broker_unverified_contracts.png "The Pactflow Broker showing as of yet unverified contracts")

**But what if I don't use Maven (or Java)?**

In this example, Maven is used to run the tests and publish the generated contracts to the Pact Broker. If you're not using Java, there are a number of [different Pact bindings for common languages](https://docs.pact.io/implementation_guides/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}, as well as the Pact command line interface (CLI) tools to enable you to upload contracts to (and retrieve them from) a Pact Broker, too. Read more about the Pact CLI tools [here](https://docs.pact.io/pact_broker/client_cli/readme/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}.

### Step 3: Provider-side contract verification

In the example shown in the [previous article](/an-introduction-to-contract-testing-part-3-getting-started-with-pact/), we specified a local folder in which the provider implementation could find the necessary contracts using the `@PactFolder` annotation. If you're using a Pact Broker (and really, you should...), you can use the `@PactBroker` annotation, instead:

{% highlight java %}
@PactBroker(url = "https://ota.pact.dius.com.au", authentication = @PactBrokerAuth(token = "YOUR_PACT_BROKER_TOKEN_GOES_HERE"))
{% endhighlight %}

Upon running the provider verification tests, Pact will pull the relevant contracts from the broker using the URL and authentication token you specified. Running

`mvn clean test`

for our Address provider yields the following output:

```
[INFO] Results:
[INFO]
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  23.637 s
[INFO] Finished at: 2021-07-05T17:36:00+02:00
[INFO] ------------------------------------------------------------------------
```

### Step 4: Uploading provider verification results

After successfully verifying that our provider implementation meets the expectations expressed in the consumer contracts, we want to inform these consumers of this success by uploading the verification results to our Pact Broker.

The most straightforward way to do is, is by setting the `pact.verifier.publishResults` flag to true when running our tests:

`mvn clean -Dpact.verifier.publishResults=true test`

This automatically publishes the verification results to the Pact Broker specified in the `@PactBroker` annotation. In our example, this leads to the following results being displayed in our Broker:

![verified contracts](/images/blog/pact_broker_successfully_verified_contracts.png "The Pactflow Broker showing our successfully verified contracts")

Alternatively, you can use the Pact CLI tools to upload the verification results to the Pact Broker, too.

### Step 5: Can I deploy?

So, now we've seen how both the consumer and the provider have done their due diligence when it comes to contract testing, and how the steps they need to perform can be automated and made part of their respective deployment pipelines. But when the time comes to actually deploy, how do you know it is safe to do so? How do you know that your current version is (still) compatible with all known versions of all parties that you'll be interacting with in production?

To help you answer this question, your Pact Broker maintains a matrix of compatible pairs of consumer and provider versions, and the environments they are deployed to. Additionally, the Pact Broker CLI tools include a tool called [can-i-deploy](https://docs.pact.io/pact_broker/can_i_deploy/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}, that checks this matrix and lets you know if the current version you're deploying to production is compatible with the production versions of the parties you have a contract with.

Regarding can-i-deploy, one obvious question that you might have when reading this is

> When should we call this command?
 
While a big part of the answer to this question is 'it depends...', it's worth mentioning that Pact tests are usually run as part of local unit testing and as part of the CI process (like any other unit test suite). Running can-i-deploy is usually done in CI/CD pipelines (CI to know when it's safe to merge, and CD to help with safely releasing new versions to a target environment).

Note: to be able to successfully use can-i-deploy, you'll want to use tags to identify versions that are currently in production (or pre-production, or...). The [can-i-deploy documentation](https://docs.pact.io/pact_broker/can_i_deploy/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"} contains a very useful tagging example.

Also, it's worth mentioning that the Pact team is currently working on features that will make deployment easier by modelling branches, environments and tags as first-class citizens in the Pact Broker. Their idea behind this is that having them modelled means you can capture the defaults (e.g. important branches and environments) and provide sensible defaults for most of this, rather than requiring users to understand all the knobs and levers and the best combination of those. More information about this upcoming feature can be found [here](https://docs.pact.io/pact_broker/recording_deployments_and_releases/?utm_source=partner&utm_campaign=on-test-automation&utm_content=blog-automation-pact-broker){:target="_blank"}.

Many thanks to [Matt](https://www.linkedin.com/in/digitalmatt/){:target="_blank"} for bringing this to my attention.

### So, all is well, now, right?

That's it! We've now successfully taken the initial contract tests for both the consumer and provider side and made them part of their respective build pipelines. All checks pass, which means we're good to go. Time to relax, right?

Well, yes... For now. As you know, software is ever evolving, and what is compatible now might not be in the future! In the next article, we'll see what happens when consumer expectations change, how Pact informs us about these changes, and what you can do to address the consequences of these changes.