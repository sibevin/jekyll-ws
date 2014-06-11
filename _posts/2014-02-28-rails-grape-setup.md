---
layout: post
title:  在rails中設置grape
date:   2014-02-28 10:42:57
categories:
tags: rails grape
---

最近用Angular JS的關係，需要建置一個API server來串angular的resource。當然用rails的controller與view也可以組出一個API來用，不過講到API framework，大家都說[grape][grape]還不錯用，速度快語法又簡潔，於是花了點研究了一下。

grape本身是架構在rack之上，所以不一定需要rails，不過網頁本身是用rails架的，也想要共用ActiveRecord的資料結構，所以還是希望可以在rails上使用grape。方式其實也很簡單，步驟如下：

在Gemfile中加入grape：

    gem 'grape'

在application.rb中加入grape的設定：

    # config/application.rb

    module MyApp
      class Application < Rails::Application
        #...
        # Setup grape api
        config.paths.add File.join('app', 'api'), glob: File.join('**', '*.rb')
        config.autoload_paths += Dir[Rails.root.join('app')]
        #...
      end
    end

* `config.paths.add` - 將app/api裡所有的檔案都加入paths中，這樣api下的module與class才可以被用rails拿來用。
* `config.autoload_paths` - 這行有點怪，因為`autoload_paths`的預設值就是app，但如果不加這一行，route會不懂app/api裡定義的class/module，所以還是要加。

在routes中加入API的route：

    # config/routes.rb

    mount Api::MyFirstApi => '/'

* 這裡定義了API的path要從哪裡開始，`Api::MyFirstApi`是定義API的class，`'/'`表示API的path開頭都是從/開始。

在app下建立一個api的目錄，加入一個api的檔案：

    # app/api/my_first_api.rb
    module Api
      class MyFirstApi < Grape::API
        version 'v0'
        prefix 'api'
        format :json

        get :hello do
         { msg: 'Grape, Hello World!!' }.as_json
        end
      end
    end

* 定義API的class都一定要繼承`Grape::API`
* `version`與`api`會定義API的path，定義在MyFirstApi中的API，path都會以/api/v0開頭。
* `format`定義了API回傳的格式。
* `get :hello`定義了一個GET的API hello，用/api/v0/hello這個path就會呼叫這裡定義的程式碼，然後吐回結果。

一切都準備好了，跑個rails s，在瀏覽器上打上/api/v0/hello就可以看到下面的結果嘍，耶～

    {"msg":"Grape, Hello World!!"}

看一下rails的log，你會發現呼叫使用grape建立的API只有短短的一行log。

    Started GET "/api/v0/hello" for 127.0.0.1 at 2014-02-28 15:57:59 +0800

使用grape的優勢在於grape比起rails render view的速度快上許多，這也是使用rails做API的最容易出現效能瓶頸的地方。

[grape]: https://github.com/intridea/grape
