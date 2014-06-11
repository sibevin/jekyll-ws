---
layout: post
title:  10 Things Make API Users Like You 的心得筆記
date:   2014-04-25 23:36:27
categories:
tags:   api
---

在RubyConf 2014 Taiwan中，David Yun分享了他在API上設計的經驗([10 Things to Make API Users Like You - 投影片在此][ref])，內容非常的實用。因為自己本身也有在規劃API，所以聽起來額外心有慼慼焉。下面是一些內容的心得與擷錄。

## 原則

1. 一致性：不管在route的設計、傳入與回傳的資料格式應該都要保持一貫的設計原則。
2. 文件：文件必須清楚易讀、即時更新、能自動生成。
3. 效率：API的反應要快、資料量要盡可能的小，也就是只傳必要的資訊就夠了。

## route的設計

1. 符合RESTful是好事，不過合理的(reasonable)route可以讓使用者更容易使用。例如(x)`DELETE /users/1/comments/1/`與(o)`DELETE /comments/1`的差別，因為刪除comment本身不需要user的資訊，為了滿足RESTful反而造成使用上的困擾。
2. 必免route暗示API的實作資訊，降低被攻擊的風險。例如(x)`GET /users/username/settings`與(o)`GET /settings`的差別，在route中帶user name的資訊就暗示了別人可以用不同的username來攻擊API。

## paging設計

offset + limit與page + per_page的比較，刪除資料時造成的差異。

## 文件

用RSpec來生成文件是個有趣的想法，寫完測試就相當於寫完文件。不過我覺得RSpec並不適合用來生文件，應該說比較適合當使用範例。在使用RSpec做測試時，正反面的例子都會寫，甚至會為了一些特殊的case來寫測試，全部都生成文件並反而會讓文件太複雜。


grape也有自動生成文件的機制，不過是針對程式本身的實作去生成API，這樣其實就足夠了。

## 效率

1. jbuilder與serializer的運用。
2. rack-rewrite - 在rack層級去覆寫route的設定，做re-direct。
3. 只傳必要的資訊，例如建立或是刪除資料時，其實回傳status code就可以了。

## 安全

1. 利用User-Agent。
2. 定義route時，使用only與except來去掉不必要的route。
3. rack-attack - 在rack層級就過濾不合法的request。

[ref]: https://speakerdeck.com/abookyun/10-things-to-make-api-users-like-you
