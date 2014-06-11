---
layout: post
title:  如何在ruby中轉換integer與bit string
date:   2014-02-20 09:52:39
categories: program
tags: ruby
---

integer轉bit string：

{% highlight ruby %}
25.to_s(2)
# "11001"
{% endhighlight %}

bit string轉integer：

{% highlight ruby %}
"11001".to_i(2)
# 25
{% endhighlight %}

這個技巧同樣可以用在8進位與16進位。
