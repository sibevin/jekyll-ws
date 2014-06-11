---
layout: post
title:  ruby class的initialize
date:   2014-02-22 11:28:58
categories:
tags: ruby ruby-basic
---

{% highlight ruby %}
class A
  def initialize
    @var_a = 1
  end
end

class B < A
  def print_var_a
    p @var_a
  end
end

B.new.print_var_a
# 1
{% endhighlight %}

1. parent class的initialize會被child class繼承。
2. parent class的static variable也會被繼承，可以在child class中使用這些變數。

{% highlight ruby %}
class C < A
  def initialize
    p @var_a
    super
    p @var_a
  end
end

C.new
# nil
# 1
{% endhighlight %}

1. 如果在child class中明確定義了另一個initialize，那parent class的initialize會被child class的initialize複寫。
2. 可以在child class的initialize中使用super來呼叫parent class的initialize。
