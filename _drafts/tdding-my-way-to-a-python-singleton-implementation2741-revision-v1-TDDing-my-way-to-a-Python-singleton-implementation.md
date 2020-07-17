---
id: 2750
title: TDDing my way to a Python singleton implementation
date: 2020-05-14T08:51:51+02:00
author: Bas
layout: revision
guid: https://www.ontestautomation.com/2741-revision-v1/
permalink: /2741-revision-v1/
---
So, I&#8217;ve been working on my first ever project as a software developer for a while now, and it&#8217;s crazy (but amazing) to see how much I have learned so far. Anything from writing readable code, to unit testing, to creating good documentation, to packaging and publishing my work, you name it, I&#8217;ve done it now. And I&#8217;m still learning, a lot, every day. So far, I&#8217;m loving it.

One of the challenges I encountered along the way was that I had to implement a singleton in Python. For those of you who don&#8217;t know what a singleton is, it&#8217;s <a href="https://en.wikipedia.org/wiki/Singleton_pattern" target="_blank" rel="noreferrer noopener">a pattern that restricts the instantiation of a class to a single instance</a>. I need this in my software because I need a single source of truth for a number of settings that are valid for the duration of a session.

Now, there are plenty of solid examples of how to do that in, for example, Java (see <a rel="noreferrer noopener" href="https://www.baeldung.com/java-singleton" target="_blank">here</a> for two examples as explained on the excellent Baeldung website), but for Python, not so much. It&#8217;s not that there aren&#8217;t any examples available online, the problem is that there are a lot of them, all implemented in different ways, and not all of them are equally readable and portable to what I needed. I&#8217;d prefer a native solution (no relying on third party libraries), since I&#8217;m developing a package for distribution myself and the fewer dependencies, the better.

So, I thought, why not try my hand at Test Driven Development (<a rel="noreferrer noopener" href="https://en.wikipedia.org/wiki/Test-driven_development" target="_blank">TDD</a>) and see where that leads? It&#8217;s a practice I would like to have more experience in anyway.. In this blog post, I&#8217;ll try and explain my thought process and what I came up with in the end. It might not be the best implementation (hey, I&#8217;m still learning, and this is my first ever attempt at TDD), and I&#8217;d love any feedback and suggestions, but it did the trick for me in tackling this challenge.

Let&#8217;s go! (This is me talking to myself for the remainder of this blog post)

The first requirement for my singleton class implementation (let&#8217;s call it _MySingleton_) was that it could be instantiated. The simplest test that checks this requirement looks like this:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_singleton_can_be_instantiated():
    my_singleton = MySingleton()
    assert my_singleton is not None</pre>

and here&#8217;s the simplest version of the class code I could think of that makes this test pass:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">class MySingleton:

    def __init__(self):
        pass</pre>

So far, so good. Our class is not a singleton yet, of course. Also, it&#8217;s not a very useful class… Before we move to the actual singleton part, let&#8217;s make sure that we can pass a parameter to the _\_\_init\_\_()_ method (comparable to the class constructor in other languages), that it is stored in a field and that it can be read again. First the test:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_singleton_can_take_an_argument():
    my_singleton = MySingleton(fruit=&#039;banana&#039;)
    assert my_singleton.fruit == &#039;banana&#039;</pre>

then the implementation:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">class MySingleton:

    def __init__(self, fruit: str):
        self.fruit = fruit</pre>

Not a lot of refactoring that can be done here, I guess, so let&#8217;s move on to the next step. Now that we have warmed up, it&#8217;s time to take a shot at making this class a proper singleton. And here&#8217;s where things get interesting… 

Typically, a singleton is created by &#8216;hiding&#8217; the constructor (i.e., making it private), but Python does not allow us to do that. So, we need to find a way around it.

What we _can_ do, though, is creating a class variable _instance_ that contains the instance, and assign the current instance of the class to it whenever the _\_\_init\_\_()_ function is called for the first time. A test for this would simply have to invoke the ___init___() method by calling _MySingleton()_ and then check that the _instance_ class variable is not equal to _None_:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_singleton_instance_is_stored_as_a_class_variable():
    my_singleton = MySingleton(fruit=&#039;pear&#039;)
    assert my_singleton.instance is not None</pre>

We can implement this requirement like this:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">class MySingleton:

    instance = None

    def __init__(self, fruit: str):
        MySingleton.instance = self</pre>

But hey… didn&#8217;t we forget something here? Yes, indeed, we also need to make sure once more that the _fruit_ field of our single instance is set and can be read. Here&#8217;s the test:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_singleton_class_variable_exposes_properties():
    my_singleton = MySingleton(fruit=&#039;apple&#039;)
    assert my_singleton.instance.fruit == &#039;apple&#039;</pre>

and here&#8217;s the implementation:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">class MySingleton:

    instance = None

    def __init__(self, fruit: str):
        MySingleton.instance = self
        MySingleton.instance.fruit = fruit</pre>

Phew. Close call! Now would be a good time to check if there&#8217;s any refactoring we can do on our class.. Nope, so far so good I think!

Next, I&#8217;d like to add another requirement: because we can&#8217;t hide the _\_\_init\_\_()_ function, I&#8217;d like to get notified whenever a process calls this function after the singleton has been created. You never know who&#8217;s going to consume your class.

I _could_ choose to simply ignore any subsequent calls to _\_\_init\_\_()_, but instead, I&#8217;d like to be explicit and raise a _RuntimeError_ whenever _\_\_init\_\_() ****_is called after our instance has been created.

Here&#8217;s what a test for that could look like (notice that testing for raised errors is straightforward with pytest):

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_singleton_cannot_be_instantiated_twice():
    MySingleton(fruit=&#039;grape&#039;)
    with pytest.raises(RuntimeError) as re:
        MySingleton(fruit=&#039;pineapple&#039;)
    assert str(re.value) == &#039;Already instantiated!&#039;</pre>

and here&#8217;s the implementation to make our test pass:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">class MySingleton:

    instance = None

    def __init__(self, fruit: str):
        if self.instance is not None:
            raise RuntimeError(&#039;Already instantiated!&#039;)
        MySingleton.instance = self
        MySingleton.instance.fruit = fruit</pre>

As a final requirement, instead of allowing consuming classes to refer to the _instance_ class variable directly, I&#8217;d like to encapsulate access to it in an _instance()_ method, so that in the future, we can put additional logic or validations in there if required.

Because we might want to allow for class modification in this method in the future, it&#8217;s best to make this method a class method, as static methods are restricted in what data they can access.

We have to test for a couple of things here:

  1. The _instance()_ method should return the initial instance
  2. The _instance()_ method should allow access to the _fruit_ field, and
  3. The _instance_ class variable (renamed to ___instance_) should no longer be directly accessible (since that would break the encapsulation)

Here are the tests that check whether all the above conditions are satisfied:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">def test_singleton_retains_initial_property_values_after_subsequent_init_calls():
    MySingleton(fruit=&#039;lychee&#039;)
    with pytest.raises(RuntimeError):
        MySingleton(fruit=&#039;orange&#039;)
    assert MySingleton.instance().fruit == &#039;lychee&#039;

def test_singleton_instance_is_accessible_using_class_method():
    MySingleton(fruit=&#039;raspberry&#039;)
    my_singleton_instance = MySingleton.instance()
    assert my_singleton_instance.fruit == &#039;raspberry&#039;

def test_singleton_instance_field_is_not_directly_accessible():
    with pytest.raises(AttributeError) as ae:
        MySingleton(fruit=&#039;peach&#039;).__instance
    assert str(ae.value) == "&#039;MySingleton&#039; object has no attribute &#039;__instance&#039;"</pre>

and here&#8217;s the final implementation of the _MySingleton_ class:

<pre class="EnlighterJSRAW" data-enlighter-language="python" data-enlighter-theme="" data-enlighter-highlight="" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-title="" data-enlighter-group="">class MySingleton:

    __instance = None

    def __init__(self, fruit: str):
        if MySingleton.__instance is not None:
            raise RuntimeError(&#039;Already instantiated!&#039;)
        MySingleton.__instance = self
        MySingleton.__instance.fruit = fruit

    @classmethod
    def instance(cls):
        return cls.__instance</pre>

Great! Our singleton is ready for use (again, I don&#8217;t think this needs a lot of refactoring at the moment), and we&#8217;ve got a number of tests to go with it, all thanks to a little bit of &#8216;thinking out loud&#8217;:

<blockquote class="wp-block-quote">
  <p>
    What do I want my code to do? How can I test for that?
  </p>
</blockquote>

For me, this has been a useful first go at applying TDD to write production code. I&#8217;ve certainly learned a lot in the process. I&#8217;m not sure if I&#8217;m going to use it all the time, but I am definitely going to practice it some more in those cases where I can&#8217;t exactly figure out how to implement something right away.

All code snippets and tests are available on my GitHub page, feel free to check them out <a rel="noreferrer noopener" href="https://github.com/basdijkstra/ota-examples/tree/master/python-tdd-singleton" target="_blank">here</a>.

_P.S. I&#8217;m looking for my next opportunity as a freelance Python developer (relatively new but learning fast), or as a freelance test automation trainer or engineer (experienced), from mid June 2020 onwards. I&#8217;m currently working on a Python development project and would definitely love to continue down this path. Feel free to get in touch or refer me to somebody you know who might be hiring. Thanks!_