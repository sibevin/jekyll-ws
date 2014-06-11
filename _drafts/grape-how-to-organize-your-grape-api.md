---
layout: post
title:  如何組織你的grape API
date:   2014-02-28 16:10:49
categories:
tags:   grape
---

設計API與一般建立網站的概念不太一樣，尤其是如果你的API是要給其它client來呼叫，例如行動裝置的App，很重要的一點是要保持API的相容性。

網頁的內容因為都是由伺服器端生成之後在丟給瀏覽器去顯示，所以隨時都可以做更新。但是App一旦發佈上架，你就沒辦法再修改App的內容，另一方面你也沒有辦法保證所有的人都會更新到最新的版本(當然你可以讓舊的App壞掉來強迫使用者做升級，不過這麼做一定會被抱怨)。因此對應App所呼叫的API在App發佈之後便不能再去修改它呼叫的方式，不然App就有可能會壞掉。

那這時候就有問題啦，要提供新的API界面給新版的App呼叫，那要怎麼辦呢？最常見的做法就是讓API帶版本，例如App v1.0.0就使用v1的API，一旦App v1.0.0發佈了，v1的API就必須是凍結狀態(frozen)，也就是不能再修改v1 API所有的界面。新界面或新功能就做在v2 API，新版本的App v2.0.0就呼叫v2版本的API。這樣就可以確保API對舊版App的相容性了。

身為API framework的grape，本身就提供了版本的功能，而且還支援四種不同的實作方式(其實就是在不同的地方帶入版本資訊)，有興趣的人可以參考[grape - versioning][grape-version]。不過又出現了一個問題，在做v2 API的時候，我們會發現有些API或是helper其實v1 v2都會共用，同樣的東西各寫在兩組API class中很明顯就違反了DRY的原則。

[grape-version]: https://github.com/intridea/grape#versioning
