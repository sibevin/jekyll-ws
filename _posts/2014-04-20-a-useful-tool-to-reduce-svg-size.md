---
layout: post
title:  svgo - 最佳化svg的好用工具
date:   2014-04-20 22:47:25
categories:
tags:   design svg
---

最近將網站的圖都換成了svg的格式，結果發現如果是比較複雜的icon，svg的檔案都會比png來的大。在尋找處理svg的相關的資訊時，發現了[svgo][svgo]這個好用的工具。它是一套最佳化svg的程式跑在npm上，安裝與使用方式可以參考它的github，README裡都寫的很詳細。

svgo本身是採用plugin的設計方式，你可以使用config來決定哪些最佳化的步驟可以省去不做。舉個例子來說，我發現被最佳化的svg沒辦法用inkscope再打開來編輯，後來做了一些測試發現是`convertPathData` plugin所造成的。如果要讓`convertPathData`省去不做，首先建立一個自定的svgo.yml，將不想執行的plugin後面加上false，以下面的例子就是將`convertPathData`設成false。

{% highlight yaml%}
    plugins:
      - removeDoctype
      - removeXMLProcInst
      - removeComments
      - removeMetadata
      - removeEditorsNSData
      - cleanupAttrs
      - convertStyleToAttrs
      - removeRasterImages
      - cleanupNumericValues
      - convertColors
      - removeUnknownsAndDefaults
      - removeNonInheritableGroupAttrs
      - removeUselessStrokeAndFill
      - removeViewBox
      - cleanupEnableBackground
      - removeHiddenElems
      - removeEmptyText
      - convertShapeToPath
      - moveElemsAttrsToGroup
      - moveGroupAttrsToElems
      - collapseGroups
      - convertPathData: false
      - convertTransform
      - removeEmptyAttrs
      - removeEmptyContainers
      - mergePaths
      - cleanupIDs
      - removeUnusedNS
      - transformsWithOnePath
      - sortAttrs
{% endhighlight %}

在使用svgo的時候，加上`--config=your/path/to/svgo.yml`就可以了。

[svgo]: https://github.com/svg/svgo
