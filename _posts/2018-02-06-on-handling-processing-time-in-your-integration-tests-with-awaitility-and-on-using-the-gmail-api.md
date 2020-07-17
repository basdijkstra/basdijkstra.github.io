---
id: 2126
title: On handling processing time in your integration tests with Awaitility, and on using the Gmail API
date: 2018-02-06T09:10:00+01:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=2126
permalink: /on-handling-processing-time-in-your-integration-tests-with-awaitility-and-on-using-the-gmail-api/
categories:
  - API testing
tags:
  - api testing
  - awaitility
  - gmail api
  - rest-assured
---
For someone that writes and talks about <a href="http://rest-assured.io/" rel="noopener" target="_blank">REST Assured</a> a lot, it has taken me a lot of time to find an opportunity to use it in an actual client project. Thankfully, my current project finally gives me the opportunity to use it on a scale broader than the examples and exercises from my workshop. Being able to do so has made me realize that some of the concepts and features I teach in the workshop deserve a more prominent spot, while others can be taught later on, or more briefly.

But that&#8217;s not what I wanted to talk about today. No, now that I actually use REST Assured for integration testing, as in: the integration between a system and its adjacent components, there&#8217;s something I have to deal with that I haven&#8217;t yet had to tackle before: processing time, or the time it takes for a message triggered using the REST API of system A to reach and be processed by system B, and being able to verify its result through consuming the REST API of system B.

Unlike writing tests for a single API, which is how I have been using and demonstrating REST Assured until now, I need a mechanism that helps me wait exactly long enough for the processing to be finished. Similar to user interface-driven automation, I could theoretically solve this by using calls to _Thread.sleep()_, but that&#8217;s ugly and vile and&#8230; just no.

Instead, I needed a mechanism that allowed me to poll an API until its response indicated a certain state change had occurred before performing the checks I needed to perform. In this case, and this is the example I&#8217;ll use in the remainder of this blog post, I invoked the REST API of system A to trigger a password reset for a given user account, and wanted to check if that resulted in a &#8216;password reset&#8217; email message arriving in system B, system B being a specific Gmail inbox here.

**Triggering the password reset**  
Triggering the password reset is done by means of a simple API call, which I perform (as expected) using REST Assured:

<pre class="brush: java; gutter: false">given().
    spec(identityApiRequestSpecification)
and().
    body(new PasswordForgottenRequestBody()).
when().
    post("/passwordreset").
then().
    assertThat().
    statusCode(204);</pre>

**Waiting until the password reset email arrives**  
As stated above, I could just include a fixed waiting period of, say, 10 seconds before checking Gmail and seeing whether the email message arrived as expected. But again, _Thread.sleep()_ is evil and dirty and&#8230; and should be avoided at all times. No, I wanted a better approach. Preferably one that didn&#8217;t result in unreadable code, both because I use my code in demos and I&#8217;d like to spend as little time as possible explaining my tests to others, and therefore want to keep it as readable as possible. Looking for a suitable library (why reinvent the wheel.. ) I was pointed to a solution that was created by Johan Haleby (not coincidentally also the creator of REST Assured), called <a href="https://github.com/awaitility/awaitility" rel="noopener" target="_blank">Awaitility</a>. From the website:

> Awaitility is a DSL that allows you to express expectations of an asynchronous system in a concise and easy to read manner.

I&#8217;m not going to write about all of the features provided by Awaitility here (the <a href="https://github.com/awaitility/awaitility/wiki/Usage" rel="noopener" target="_blank">usage guide</a> does that way better than I ever could), but to demonstrate its expression power, here&#8217;s how I used it in my test:

<pre class="brush: java; gutter: false">await().
    atMost(10, TimeUnit.SECONDS).
with().
    pollInterval(1, TimeUnit.SECONDS).
    until(() -&gt; this.getNumberOfEmails() == 1);</pre>

This does exactly what it says on the tin: it executes a method called _getNumberOfEmails()_ once per second for a duration of 10 seconds, until the result returned by that method equals 1 (in which case my test execution continues) or until the 10 second timeout period has been exceeded, resulting in an exception being thrown. All with a single line of readable code. That&#8217;s how powerful it is.

In this example, the _getNumberOfEmails()_ is a method that retrieves the contents for a specific Gmail mailbox and returns the number of messages in it. Before the test starts, I empty the mailbox completely to make sure that no old messages remain there and cause false positives in my test. Here&#8217;s how it looks:

<pre class="brush: java; gutter: false">private int getNumberOfEmails() {

    return given().
        spec(gmailApiRequestSpec).
    when().
        get("/messages").
    then().
        extract().
        path("resultSizeEstimate");
}</pre>

This method retrieves the number of emails in a Gmail inbox (the required OAuth2 authentication details, base URL and base path are specified in the _gmailApiRequestSpec_ RequestSpecification) by means of a GET call to _/messages_ and extracting and returning the value of the _resultSizeEstimate_ field of the JSON response returned by the API. If you want to know more about the Gmail API, its documentation can be found <a href="https://developers.google.com/gmail/api/" rel="noopener" target="_blank">here</a>, by the way.

**Checking the content of the password reset message**  
So, now that we know that an email message has arrived in the Gmail inbox, all that&#8217;s left for us to do is check whether it is a password reset message and not any other type of email message that might have arrived during the execution of our test. All we need to do is to once more retrieve the contents of the mailbox, extract the message ID of the one email message in it, use that to retrieve the details for that message and check whatever we want to check (in this case, whether it has landed in the inbox and whether the subject line has the correct value):

<pre class="brush: java; gutter: false">String messageID =
    given().
        spec(gmailApiRequestSpec).
    when().
        get("/messages").
    then().
        assertThat().
        body("resultSizeEstimate", equalTo(1)).
    and().
        extract().
        path("messages.id[0]");

    // Retrieve email and check its contents
    given().
        spec(gmailApiRequestSpec).
    and().
        pathParam("messageID", messageID).
    when().
        get("/messages/{messageID}").
    then().
        assertThat().
        body("labelIds",hasItem("INBOX")).
    and().
        body("payload.headers.find{it.name==&#039;Subject&#039;}.value",equalTo("Password reset"));</pre>

**Gmail authentication**  
It took me a little while to figure out how to consume the Gmail API. In the end, this proved to be quite simple but I spent a couple of hours fiddling with OAuth2, authentication codes, OAuth2 access and refresh tokens and the way Google has implemented this. Describing how all this goes is beyond the scope of this blog post, but you can find instructions <a href="https://developers.google.com/gmail/api/guides/" rel="noopener" target="_blank">here</a>. Once you&#8217;ve obtained a refresh token, store it, because that&#8217;s the code you can use to generate a new access token through the API, without having to deal with the pesky authentication user interface. For those of you more experienced with OAuth2, this might sound obvious, but it took me a while to figure it out. Still, it&#8217;s far better than writing automation against the Gmail user interface, though (seriously, DON&#8217;T DO THAT).

So, to wrap things up, there are two lessons here.

One, if you&#8217;re looking for a library that helps you deal with processing times in integration tests in a flexible and readable manner, and you&#8217;re writing your tests in Java, I highly recommend taking a look at Awaitility. I&#8217;ve only recently discovered it but I&#8217;m sure this one won&#8217;t leave my tool belt anytime soon.

Two, if you want to include checking email into your integration or possibly even your end-to-end tests, skip the user interface and go the API route instead. Alternatively, you could try an approach like Angie Jones presents in a <a href="http://angiejones.tech/test-automation-to-verify-email/" rel="noopener" target="_blank">recent blog post</a>, leveraging the JavaMail API, instead. Did I say don&#8217;t use the Gmail (or Outlook, or Yahoo, or whatever) user interface?