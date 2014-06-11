---
layout: post
title:  在pow中設定debugger
date:   2014-05-05 09:40:47
categories:
tags:   rails debug
---

如果你用mac開發rails，那你一定知道pow這個簡單好用的web server，不過如果要用debugger在pow上面做debug，那就要做一些額外的設定。這裡做一下記錄。

# 設定

首先在Gemfile中加入`gem 'debugger'`

    group :development do
      # ...
      gem 'debugger'
      # ...
    end

接著在config/environments/development.rb中加入debugger的設定

    QlbApp::Application.configure do
      #...
      # Setup debugger for pow
      if Debugger
        Debugger.settings[:autoeval] = true
        Debugger.settings[:autolist] = 1
        Debugger.settings[:reload_source_on_change] = true
        Debugger.start_remote
      end
      # ...
    end

重啟pow後，基本上設定就完成了。

# DEBUG

如果要測試的話，首先在另一個console下指令`bundle exec rdebug -c`，出現Connected的訊息表示debugger成功的與pow連結，可以準備debug了。

    $ bundle exec rdebug -c
    Connected.

接著只要在想要插入breakpoint的地方加上`debugger`即可，這時候rails跑到這裡就會停住，可以用debug console進行debug了。

# 一直無法進入breakpoint

用了debugger一段時間之後，發現breakpoint有時候會被跳過，重啟了pow還是一樣，後來在[SO][ref]上找到了問題的答案。結論就是pow預設會啟兩個process在執行，如果開了debugger之後，debugger只會對其中一個process連線，這時候如果是另一個process來處理request的話，就無法順利進入breakpoint。

解決的方法就是讓pow啟動時，預設只啟動一個process，這樣就不會有這個問題了。方法也很簡單，就是在~/.powconfig下加入下面的設定：

    export POW_WORKERS=1

接著重啟pow之後就好了。

# Ref

* [stackoverflow - ruby-debug with Pow — breakpoints never hit][ref]

[ref]: http://stackoverflow.com/q/8996498/232710
