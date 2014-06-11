---
layout: post
title:  Random Token
date:   2014-05-14 10:01:17
categories:
tags:   masterpiece gem
---

* Github: [https://github.com/sibevin/random_token][github]
* RubyGems: [http://rubygems.org/gems/random_token][gem]

當初只是想要做一個方便用來生成亂數字串的工具，沒有想說要做多完整的功能，另一方面也怎嘗試怎麼做一個gem。沒想到做了之後就會開始欲罷不能的多做一些功能，像是string format或是指定亂數機率分佈等。

做這個gem最大的收獲就是實作string format，如何去parse傳入的string有哪些directive，然而轉成想要的option。看似困難，其實實際下去做感覺反而沒想像的這麼難。另一個收獲是學習使用ruby內建的test framework，之前在寫rails都是用rspec，但如果只是一個gem，用rspec似乎有點殺雞牛刀的感覺，還要增加dependence，所以就試著用ruby的unit test來做測試。感覺跟寫rspec很像，不過每個case前都要用test_開頭的語法有點多餘就是了。

可惜的是這個gem似乎沒什麼人在用啊，也許是自己生亂數的程式碼也沒有很難，大部分的人應該google一下就找到[做法][so]了。不過如果你需要的字串產生的方式是非常特別，例如要過濾一些特別的字元，或是需要指定亂數機率分佈，或許這個gem可以幫上一點忙。

[github]: http://rubygems.org/gems/random_token
[gem]: http://rubygems.org/gems/random_token
[so]: http://stackoverflow.com/questions/88311/how-best-to-generate-a-random-string-in-ruby
