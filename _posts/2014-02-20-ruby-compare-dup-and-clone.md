---
layout: post
title:  dup與clone的差異
date:   2014-02-20 10:08:54
categories: program
tags: ruby
---

* `clone`會複製singleton class的method與constants，`dup`不會

{% highlight ruby %}
o = Object.new
class << o
  A = 'aa'
  def self.b
    'bb'
  end
end

o.clone.A # "aa"
o.dup.A # raise NoMethodError
o.clone.b # "bb"
o.dup.b # raise NoMethodError
{% endhighlight %}

* `clone`會將freeze的狀態保留下來，`dup`不會

{% highlight ruby %}
class Foo
  attr_accessor: bar
end
foo = Foo.new
foo.freeze

foo.clone.bar = 'cc' # ok
foo.dup.bar = 'cc' # raise RuntimeError
{% endhighlight %}

### Refs

* [stackoverflow - What's the difference between Ruby's dup and clone methods?][ref]

[ref]: http://stackoverflow.com/questions/10183370/whats-the-difference-between-rubys-dup-and-clone-methods

