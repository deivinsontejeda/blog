---
layout: post
title: "JRuby bug fixed with Regexp class"
date: 2013-08-15 17:33
comments: true
categories: jruby bug fixed
---

I have had some days funny playing with [JRuby](http://jruby.org/), specifically I'm working in a DSL [verbal_expressions](https://github.com/ryan-endacott/verbal_expressions) to parser URL.

**verbal_expressions** is so sexy, very verbose as way it was built and as you can use it. I tested it in MRI and works fine, but in my current project, we need developing a set new features upon JVM, by this reason JRuby plays an important role.

When I made tests using JRuby, I found an weird behaviour, assuming that JRuby allow carry (almost) the whole Ruby code wrote using MRI.

Basically, I made the following code.

{% codeblock lang:ruby %}
class Foo < Regexp
  def initialize(&block)
    instance_eval &block
    super('/some_regex/')
  end
  def some_method(value)
    puts "Output: #{value}"
  end
end
{% endcodeblock %}

Because I went to use the previous code just like this.

{% codeblock lang:ruby %}
Foo.new { some_method 'Hi!' }
{% endcodeblock %}

As you can see is very simple what I want, just inherit from Regexp, nothing out of usual here. But when I ran the previous code get the following output.

{% codeblock %}
ArgumentError: block not supplied
    from org/jruby/RubyBasicObject.java:1709:in `instance_eval'
    from (irb):10:in `initialize'
    from org/jruby/RubyRegexp.java:729:in `new'
    from (irb):17:in `evaluate'
    from org/jruby/RubyKernel.java:1066:in `eval'
    from org/jruby/RubyKernel.java:1409:in `loop'
    from org/jruby/RubyKernel.java:1174:in `catch'
    from org/jruby/RubyKernel.java:1174:in `catch'
{% endcodeblock %}

With idea to discard some problem in my code I decided test it with a dummy code like follow:

{% codeblock lang:ruby %}
class Dummy
  def initialize(&block)
    instance_eval &block
  end
end
{% endcodeblock %}

When I run the previous code:

{% codeblock %}
>> Dummy.new { puts "yay #{self.inspect}" }
yay #<Dummy:0x54bbb2d0>
{% endcodeblock %}

Works as expected. In [JRuby's IRC](https://github.com/jruby/jruby/wiki/IRC) I explained the previous ones, after [ratnikov](https://github.com/ratnikov) made the tests, he confirms it was an issue in RubyRegexp implementation.

Basically, [RubyRegexp](https://github.com/jruby/jruby/blob/master/core/src/main/java/org/jruby/RubyRegexp.java) [was ignoring the new's block](https://github.com/jruby/jruby/commit/6729d3be83e8a7feead3e636d8bb8018c6af2dbb#L0L907)



