---
layout: post
title:  ruby class的visiablity
date:   2014-03-27 10:29:08
categories:
tags:   ruby ruby-basic
---

{% highlight ruby %}
    class A
      def a_public
        p "a_public"
      end

      def a_call_protected_implicitly
        a_protected
      end

      def a_call_protected_explicitly
        self.a_protected
      end

      def a_call_private_implicitly
        a_private
      end

      def a_call_private_explicitly
        self.a_private
      end

      protected

      def a_protected
        p "a_protected"
      end

      private

      def a_private
        p "a_private"
      end
    end

    a = A.new
    a.a_public
    #a.a_protected # protected method `a_protected' called for #<A:0xa0e98b8> (NoMethodError) *1
    #a.a_private # private method `a_private' called for #<A:0x9899924> (NoMethodError) *2
    a.a_call_protected_implicitly # *3
    a.a_call_protected_explicitly # *4
    a.a_call_private_implicitly # *5
    #a.a_call_private_explicitly # private method `a_private' called for #<A:0x82d9904> (NoMethodError) *6
{% endhighlight %}

1. 你不能在class的外部呼叫protected method。
2. 你不能在class的外部呼叫privated method。
3. 你可以用implicit的方式(也就是不明確指定receiver)在class內呼叫protected method。
4. 你可以用explicit的方式(也就是指定receiver，在上面的例子就是self)在class內呼叫protected method。
5. 你可以用implicit的方式在class內呼叫private method。
6. 你不能用explicit的方式在class內呼叫private method。
