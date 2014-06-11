---
layout: post
title:  ruby在if中的assignment
date:   2014-03-25 12:28:57
categories:
tags:   ruby
---

如果我們想要檢查`foo.get_value()`有沒有值，如果有的話就用回傳的值做什麼事，如果回傳是`nil`則不做事之類的。那我們通常會寫成下面這個樣子：

{% highlight ruby %}
    if a = foo.get_value()
      # Do something with a if Foo.get_value() return a value...
    end
{% endhighlight %}

不過當你有兩個以上的判斷式就要小心了，先看一個最簡單的例子：

{% highlight ruby %}
    if a = 1
      p a
    end

    # 1
{% endhighlight %}

這個沒有問題，`a`的值是`1`，所以會跑`p a`，印出的值是`1`。那下面這個例子？

{% highlight ruby %}
    if a = 1 && b = 2
      p a
      p b
    end

    # 2 <= ???
    # 2
{% endhighlight %}

這是因為`&&`的優先權大於`=`，所以`a = 1 && b = 2`的結果相當於

{% highlight ruby %}
    a = 1 && b = 2
    a = (1 && (b = 2))
    a = (1 && 2) # b = 2
    a = 2
{% endhighlight %}

如果想要得到`a = 1`與`b = 2`的結果，可以使用`and`(`and`的優先權比`=`低)或是`()`來得到正確的值：

{% highlight ruby %}
    if a = 1 and b = 2
      p a
      p b
    end

    # 1
    # 2

    if (a = 1) && (b = 2)
      p a
      p b
    end

    # 1
    # 2
{% endhighlight %}

