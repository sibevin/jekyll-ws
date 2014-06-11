---
layout: post
title:  如何做到before_render
date:   2014-02-23 13:00:36
categories:
tags: rails rails-controller
---

在顯示每一頁的title時，我想使用一些定義在controller中的變數當作title。於是寫了一個`ApplicationController`的method如下：

{% highlight ruby %}
class ApplicationController < ActionController::Base
  #...
  before_filter :gen_title
  #...
  private
  def gen_title
    app_name = I18n.t('app.name')
    if @brand_content && @brand_content[:title]
      @gen_title = "#{app_name} - #{@brand_content[:title]}"
    elsif @page_title
      @gen_title = "#{app_name} - #{@page_title}"
    else
      @gen_title = app_name
    end
  end
  #...
end
{% endhighlight %}

可是因為`@brand_content`與`@page_title`這兩個變數都是在controller中被設定，如果`gen_title`放在`before_filter`，則這兩個變數會沒有機會被設定而造成它們永遠都是nil。

呼叫`gen_title`最好的時間點是在controller跑完要做render的這個時間點，不過rails似乎沒有這種filter。還好SO上有人提供個好方法，就是覆寫`render`這個method，方式如下：

{% highlight ruby %}
class ApplicationController < ActionController::Base
  #...
  def render *args
    gen_title
    super
  end
  #...
  private
  def gen_title
    app_name = I18n.t('app.name')
    if @brand_content && @brand_content[:title]
      @gen_title = "#{app_name} - #{@brand_content[:title]}"
    elsif @page_title
      @gen_title = "#{app_name} - #{@page_title}"
    else
      @gen_title = app_name
    end
  end
  #...
end
{% endhighlight %}

當然如果在`ApplicationController`中覆寫`render`，就表示會改到所有的render行為（以我這個例子而言是沒有問題，因為每個頁面應該都要`gen_title`）。如果只針對需要的頁面才做的話，就在一般的controller中覆寫`render`就好。

### Refs

* [stackoverflow - Filter to execute before render but after controller?][ref]

[ref]: http://stackoverflow.com/questions/9281224/filter-to-execute-before-render-but-after-controller

