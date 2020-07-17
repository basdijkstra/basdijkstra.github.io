---
id: 930
title: 'On designing a self-diagnosing and self-healing automated test framework &#8211; part 1'
date: 2015-07-08T13:13:41+02:00
author: Bas
layout: post
guid: http://www.ontestautomation.com/?p=930
permalink: /on-designing-a-self-diagnosing-and-self-healing-automated-test-framework-part-1/
categories:
  - General test automation
tags:
  - java
  - robustness
  - self-diagnosis
  - self-healing
  - test automation
---
_This is the first post in a short series on designing robust, self-diagnosing and, if possible, even self-healing tests. You can read all articles in this series by clicking <a href="http://www.ontestautomation.com/tag/self-diagnosis/" target="_blank">here</a>._

I like automating tests. So much that I have even made my profession out of it. What I don&#8217;t like though is wasting a lot of time trying to find out why an automated test failed and whether the failure was a genuine bug, a flaky test or some sort of test environment issue.

A quick Google search showed me that there are a couple of commercial test tools that claim they come with some sort of self diagnosing and even a self healing mechanism, for example <a href="http://origsoft.com/products/testdrive/" target="_blank">this one</a>. I&#8217;m very interested to see how such a mechanism works. Skeptic as I am, I don&#8217;t really believe it until I&#8217;ve seen it. Nonetheless, I think it would be interesting to see what can be done to make existing automated test frameworks more robust by improving their ability to self-diagnose and possible even self-heal in case an unexpected error occurs. So that&#8217;s what I&#8217;ll try to do in the next couple of posts.

The step by step approach I want to follow and the considerations to be made along the way are (very) loosely inspired on a scientific article titled &#8216;A Self-Healing Approach for Object-Oriented Applications&#8217;, which can be downloaded from <a href="http://www.few.vu.nl/~rhu300/publications-2005/saacs05.pdf" target="_blank">here</a>. This article presents an approach and architecture for fault diagnosis and self-healing of interpreted object-oriented applications. As the development and maintenance of automated tests should be treated as any other software development project, I see no reason why the principles and suggestions presented in the article could not apply to a test automation framework, at least to some extent&#8230;

**When is a system self-diagnosing and self-healing?**  
Let&#8217;s start with the end in mind, as Stephen Covey so eloquently put it. What are we aiming for? In order for a system to be self-diagnosing, it should:

  1. Be self-aware, or more concretely, always have a deterministic state
  2. Recognize the fact that an error has occurred
  3. Have enough knowledge to stabilize itself
  4. Be able to analyze the problem situation
  5. Make a plan to heal itself
  6. Suggest healing solutions to the system administrator (in this case, the person responsible for test automation)

If we want our system not only to be self-diagnosing but also self-healing, the system should also:

<li value="7">
  Heal itself without human intervention
</li>

In this post &#8211; and probably some future posts as well &#8211; I will try and see whether it is possible to design a generic approach for creating robust, self-diagnosing and self-healing test automation frameworks. I&#8217;ll try and include meaningful examples based on a relatively simple Selenium test framework wherever possible.

**Self-aware tests**  
The most straightforward way to make any piece of software self-aware is to introduce the concept of _state_. A robust program, and therefore an automated test as well, should always be in a specific state when it is executing. For robustness, we will assume that the state model for an automated test is deterministic, i.e., a test can never be in more than one state, and an event that triggers a state transition should always result in the test ending up in a single new state. Let&#8217;s say we identify the following states that an automated test can be in:

  * Not running
  * Initialization
  * Running
  * Error
  * Teardown

The state model or state transition diagram could then look like this:

[<img src="http://www.ontestautomation.com/wp-content/uploads/2015/07/test_automation_state_1.png" alt="A first state model for our automated test" width="956" height="371" class="aligncenter size-full wp-image-940" srcset="https://www.ontestautomation.com/wp-content/uploads/2015/07/test_automation_state_1.png 956w, https://www.ontestautomation.com/wp-content/uploads/2015/07/test_automation_state_1-300x116.png 300w" sizes="(max-width: 956px) 100vw, 956px" />](http://www.ontestautomation.com/wp-content/uploads/2015/07/test_automation_state_1.png)

A sample implementation of this state model (also known as a finite state machine or FSM) can be created using a Java enum:

<pre class="brush: java; gutter: false">public enum State {
	
	NOT_RUNNING {
        @Override
        State doTransition(String input) {
            System.out.println("Going from State.NOT_RUNNING to State.INITIALIZATION");
            return INITIALIZATION;
        }
    },
    INITIALIZATION {
        @Override
        State doTransition(String input) {
        	if (input.equals("error")) {
        		System.out.println("Going from State.INITIALIZATION to State.ERROR");
        		return ERROR;
        	} else {
        		System.out.println("Going from State.INITIALIZATION to State.RUNNING");
        		return RUNNING;
        	}
        }
    },
    // The RUNNING and TEARDOWN states are implemented in the same way as INITIALIZATION state
    ERROR {
        @Override
        State doTransition(String input) {
        	if (input.equals("ok")) {
        		System.out.println("Going from State.ERROR to State.NOT_RUNNING");
        		return NOT_RUNNING;
        	} else {
        		System.out.println("Remaining in State.ERROR");
        		return this;
        	}
        }
    };
 
    abstract State doTransition(String input);
}</pre>

In a next post, I will show how we can apply this state model to a simple Selenium WebDriver test to make it more robust. I will also demonstrate how this state model helps us in letting our tests fail gracefully and in determining what exactly constitutes an error (versus a failed check, for example).