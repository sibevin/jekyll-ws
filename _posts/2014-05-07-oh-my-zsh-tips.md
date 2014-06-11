---
layout: post
title:  oh-my-zsh一些實用小技巧
date:   2014-05-07 22:12:34
categories:
tags:   zsh
---

聽說有一個zsh shell framework叫[oh-my-zsh][oh_my_zsh]超級好用的，它包括了一系列plugin與theme，於是就裝來試試，結果就回不去了。安裝的方式也很簡單，按照README的步驟安裝即可。

[oh_my_zsh]: https://github.com/robbyrussell/oh-my-zsh

# 設定rvm

安裝完後，如果有用rvm，要記得將rvm的設定複製到.zshrc，如下所示：

    PATH=$PATH:$HOME/.rvm/bin
    [[ -s "$HOME/.rvm/scripts/rvm"]] && source "$HOME/.rvm/script/rvm"

# 一些方便的技巧

## cd可以省略

使用shell最常做的動作的就是用cd切換目錄，在zsh中只輸入路徑按下Enter，就相當於cd到輸入的目錄。

    ex-app $ app/views/home[Enter]
    ex-app/app/views/home $

## 跳回上層目錄

通常要跳回上一層，我們會用`cd ..`，跳回上兩層是`cd ../..`，跳回上三層是`cd ../../..`，可是`..`打久了也是很煩人的。在zsh中，`cd ../..`可以用`cd ...`(..後多一個.)，`cd ../../..`可以用`cd ....`(..後多二個.)。

## 路徑模糊搜尋

任何需要路徑的地方都可以使用模糊搜尋，再按[Tab]自動補完完整的路徑。

    $ vim a/vi/home/ind[Tab]
    $ vim app/views/home/index.html.erb

## 回到前一個路徑

另一個很常用的就是`cd -`，但一般的shell只能跳回前一個路徑。在zsh中，輸入`cd -`再按下[Tab]，會列出前幾次的路徑，選擇對應的號碼就可以跳到對應的路徑。

    $ cd app[Enter]
    app $

    app $ cd views/home[Enter]
    app/views/home $

    app/views/home $ cd -[Tab]
    1 -- /app/
    2 -- /

    app/views/home $ cd -[2][Enter]
    1 -- /app/
    2 -- /
    $

## 前次指令的模糊搜尋

當要執行之前執行過的指令，我們通常用`history`來查詢，再用`!`加上歷史記錄代碼來執行。在zsh中，可以在`!`後加上指令的模糊搜尋，再按[Tab]就會切換成之前的指令。

    $ !mys[Tab]
    $ mysql -uex_dev -pex_dev ex_dev

## 用process的名稱來kill

通常要kill一個process，我們都要先找出對應的PID，再用kill PID來砍掉它。在zsh中，可以直接在kill後面接process的名稱，再按[Tab]就會切換成對應的PID。

    $ kill pow[Tab]
    $ kill 33785

## 用*列出多個檔案

在zsh中，可以用*當做萬用字元將多個符合條件的檔案路徑一起找出來。

    $ ls **/*user*[Enter]
    app/models/user.rb
    db/migrate/20140104024658_create_users.rb
    spec/factories/users.rb
    spec/models/user_spec.rb

    app/controllers/users:
    sessions_controller.rb

    app/views/users:
    sessions

## [builtin] Zmv

mv的超級強化版，可以做到批次改名。

設定：zsh預設並沒有載入zmv這個module，必須下指令`autoload -U zmv`才可以使用它。你可以將這段指令加到你的`.zshrc`中。

最常用的方式是使用`(*)`搭配`$1`,`$2`等變數，舉個例子，如果我們有下面三個檔案：

    pic01-001.png
    pic02-002.png
    pic03-003.png

我們想將`-`換成`_`，也就是改成下面的檔名

    pic01_001.png
    pic02_002.png
    pic03_003.png

用zmv只要一行就搞定了：`zmv 'pic(*)-(*)' 'pic$1_$2'`，超簡單的吧。

另外如果只想看更改名稱的結果，但不要真的更改名稱，可以加上`-n`。

更多例子可以參考：[http://strcat.de/zsh/#zmv][zmv]

## [plugin] Jump

Jump plugin最主要的功能就是在shell中加入路徑書籤。

設定：在`.zshrc`中的plugin設定加入`jump`。

* 用`mark 書籤名稱`將目前所在目錄加上書籤。
* 用`unmark 書籤名稱`刪除書籤。
* 用`marks`列出目前所有的書籤。
* 用`jump 書籤名稱`跳至書籤對應的目錄。

## [plugin] Pow

加了一些控制pow的快捷指令。

設定：在`.zshrc`中的plugin設定加入`pow`。

* 在rails app的目錄下，用`kapow`執行`touch tmp/restart.txt`。
* 在rails app的目錄下，用`powit`執行將app掛載到pow下執行。

如果你對其它的plugin有興趣的話，可以參考[plugin目錄][plugin_dir]中的plugin原始檔，還可以趁機學習一下怎麼寫出屬於自己的plugin。如果有什麼好用的技巧也歡迎大家分享。

[plugin_dir]: https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins
[zmv]: http://strcat.de/zsh/#zmv
