---
title: TDDing my way to a Python singleton implementation
layout: post
permalink: /tdding-my-way-to-a-python-singleton-implementation/
categories:
  - Development and testing
tags:
  - pytest
  - python
  - singleton
  - test driven development
---
So, I've been working on my first ever project as a software developer for a while now, and it's crazy (but amazing) to see how much I have learned so far. Anything from writing readable code, to unit testing, to creating good documentation, to packaging and publishing my work, you name it, I've done it now. And I'm still learning, a lot, every day. So far, I'm loving it.

One of the challenges I encountered along the way was that I had to implement a singleton in Python. For those of you who don't know what a singleton is, it's <a href="https://en.wikipedia.org/wiki/Singleton_pattern" target="_blank" rel="noreferrer noopener">a pattern that restricts the instantiation of a class to a single instance</a>. I need this in my software because I need a single source of truth for a number of settings that are valid for the duration of a session.

Now, there are plenty of solid examples of how to do that in, for example, Java (see <a rel="noreferrer noopener" href="https://www.baeldung.com/java-singleton" target="_blank">here</a> for two examples as explained on the excellent Baeldung website), but for Python, not so much. It's not that there aren't any examples available online, the problem is that there are a lot of them, all implemented in different ways, and not all of them are equally readable and portable to what I needed. I'd prefer a native solution (no relying on third party libraries), since I'm developing a package for distribution myself and the fewer dependencies, the better.

So, I thought, why not try my hand at Test Driven Development (<a rel="noreferrer noopener" href="https://en.wikipedia.org/wiki/Test-driven_development" target="_blank">TDD</a>) and see where that leads? It's a practice I would like to have more experience in anyway.. In this blog post, I'll try and explain my thought process and what I came up with in the end. It might not be the best implementation (hey, I'm still learning, and this is my first ever attempt at TDD), and I'd love any feedback and suggestions, but it did the trick for me in tackling this challenge.

Let's go! (This is me talking to myself for the remainder of this blog post)

The first requirement for my singleton class implementation (let's call it `MySingleton`) was that it could be instantiated. The simplest test that checks this requirement looks like this:

{% highlight python %}
def test_singleton_can_be_instantiated():
    my_singleton = MySingleton()
    assert my_singleton is not None
{% endhighlight %}

and here's the simplest version of the class code I could think of that makes this test pass:

{% highlight python%}
class MySingleton:

    def __init__(self):
        pass
{% endhighlight %}

So far, so good. Our class is not a singleton yet, of course. Also, it's not a very useful class… Before we move to the actual singleton part, let's make sure that we can pass a parameter to the `__init__()` method (comparable to the class constructor in other languages), that it is stored in a field and that it can be read again. First the test:

{% highlight python %}
def test_singleton_can_take_an_argument():
    my_singleton = MySingleton(fruit="banana")
    assert my_singleton.fruit == "banana"
{% endhighlight %}

then the implementation:

{% highlight python %}
class MySingleton:

    def __init__(self, fruit: str):
        self.fruit = fruit
{% endhighlight %}

Not a lot of refactoring that can be done here, I guess, so let's move on to the next step. Now that we have warmed up, it's time to take a shot at making this class a proper singleton. And here's where things get interesting… 

Typically, a singleton is created by 'hiding' the constructor (i.e., making it private), but Python does not allow us to do that. So, we need to find a way around it.

What we _can_ do, though, is creating a class variable `instance` that contains the instance, and assign the current instance of the class to it whenever the `__init__()` function is called for the first time. A test for this would simply have to invoke the `__init__()` method by calling `MySingleton()` and then check that the `instance` class variable is not equal to `None`:

{% highlight python %}
def test_singleton_instance_is_stored_as_a_class_variable():
    my_singleton = MySingleton(fruit="pear")
    assert my_singleton.instance is not None
{% endhighlight %}

We can implement this requirement like this:

{% highlight python %}
class MySingleton:

    instance = None

    def __init__(self, fruit: str):
        MySingleton.instance = self</pre>
{% endhighlight %}

But hey… didn't we forget something here? Yes, indeed, we also need to make sure once more that the `fruit` field of our single instance is set and can be read. Here's the test:

{% highlight python %}
def test_singleton_class_variable_exposes_properties():
    my_singleton = MySingleton(fruit="apple")
    assert my_singleton.instance.fruit == "apple"
{% endhighlight %}

and here's the implementation:

{% highlight python %}
class MySingleton:

    instance = None

    def __init__(self, fruit: str):
        MySingleton.instance = self
        MySingleton.instance.fruit = fruit
{% endhighlight %}

Phew. Close call! Now would be a good time to check if there's any refactoring we can do on our class.. Nope, so far so good I think!

Next, I'd like to add another requirement: because we can't hide the `__init__()` function, I'd like to get notified whenever a process calls this function after the singleton has been created. You never know who's going to consume your class.

I _could_ choose to simply ignore any subsequent calls to `__init__()`, but instead, I'd like to be explicit and raise a `RuntimeError` whenever `__init__()` is called after our instance has been created.

Here's what a test for that could look like (notice that testing for raised errors is straightforward with pytest):

{% highlight python %}
def test_singleton_cannot_be_instantiated_twice():
    MySingleton(fruit="grape")
    with pytest.raises(RuntimeError) as re:
        MySingleton(fruit="pineapple")
    assert str(re.value) == "Already instantiated!"
{% endhighlight %}

and here's the implementation to make our test pass:

{% highlight python %}
class MySingleton:

    instance = None

    def __init__(self, fruit: str):
        if self.instance is not None:
            raise RuntimeError("Already instantiated!")
        MySingleton.instance = self
        MySingleton.instance.fruit = fruit
{% endhighlight %}

As a final requirement, instead of allowing consuming classes to refer to the `instance` class variable directly, I'd like to encapsulate access to it in an `instance()` method, so that in the future, we can put additional logic or validations in there if required.

Because we might want to allow for class modification in this method in the future, it's best to make this method a class method, as static methods are restricted in what data they can access.

We have to test for a couple of things here:

  1. The `instance()` method should return the initial instance
  2. The `instance()` method should allow access to the `fruit` field, and
  3. The `instance` class variable (renamed to `__instance`) should no longer be directly accessible (since that would break the encapsulation)

Here are the tests that check whether all the above conditions are satisfied:

{% highlight python %}
def test_singleton_retains_initial_property_values_after_subsequent_init_calls():
    MySingleton(fruit="lychee")
    with pytest.raises(RuntimeError):
        MySingleton(fruit="orange")
    assert MySingleton.instance().fruit == "lychee"

def test_singleton_instance_is_accessible_using_class_method():
    MySingleton(fruit="raspberry")
    my_singleton_instance = MySingleton.instance()
    assert my_singleton_instance.fruit == "raspberry"

def test_singleton_instance_field_is_not_directly_accessible():
    with pytest.raises(AttributeError) as ae:
        MySingleton(fruit="peach").__instance
    assert str(ae.value) == ""MySingleton" object has no attribute "__instance""
{% endhighlight %}

and here's the final implementation of the `MySingleton` class:

{% highlight python %}
class MySingleton:

    __instance = None

    def __init__(self, fruit: str):
        if MySingleton.__instance is not None:
            raise RuntimeError("Already instantiated!")
        MySingleton.__instance = self
        MySingleton.__instance.fruit = fruit

    @classmethod
    def instance(cls):
        return cls.__instance
{% endhighlight %}

Great! Our singleton is ready for use (again, I don't think this needs a lot of refactoring at the moment), and we've got a number of tests to go with it, all thanks to a little bit of 'thinking out loud':

<blockquote class="wp-block-quote">
  <p>
    What do I want my code to do? How can I test for that?
  </p>
</blockquote>

For me, this has been a useful first go at applying TDD to write production code. I've certainly learned a lot in the process. I'm not sure if I'm going to use it all the time, but I am definitely going to practice it some more in those cases where I can't exactly figure out how to implement something right away.

All code snippets and tests are available on my GitHub page, feel free to check them out <a rel="noreferrer noopener" href="https://github.com/basdijkstra/ota-examples/tree/master/python-tdd-singleton" target="_blank">here</a>.

_P.S. I'm looking for my next opportunity as a freelance Python developer (relatively new but learning fast), or as a freelance test automation trainer or engineer (experienced), from mid June 2020 onwards. I'm currently working on a Python development project and would definitely love to continue down this path. Feel free to get in touch or refer me to somebody you know who might be hiring. Thanks!_