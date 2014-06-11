---
layout: post
title:  如何顯示utf8的欄位
date:   2014-03-04 13:10:15
categories:
tags:   mysql
---

用client連進mysql的時候，很常遇到db明明己經設定成utf8，可是透過client看utf8的欄位時，還是出現???的字串。

    +----+-----------------+------------+
    | id | name            | name_zh_tw |
    +----+-----------------+------------+
    | 1  | taipei-city     | ???        |
    | 2  | kaoh-siung-city | ???        |

通常是client characterset沒有設好，設定步驟如下：

首先，先確定mysql的characterset，下指令status;。這時會發現Db characterset是utf8，但Client characterset卻是latin1，這就會使client無法正確顯示utf8的欄位。

    Server characterset: latin1
    Db characterset: utf8
    Client characterset: latin1
    Conn. characterset: latin1

修改~/.my.cnf(如果沒有這個檔案，請從/etc/mysql/my.cnf複製一份到你的home下面)，在`[client]`的地方加入以下設定：

    [client]

    default-character-set = utf8

再用指令status;，會發現Client characterset己改成utf8，utf8的欄位也可以正常顯示了。

    Server characterset: latin1
    Db characterset: utf8
    Client characterset: utf8
    Conn. characterset: utf8

    +----+-----------------+------------+
    | id | name            | name_zh_tw |
    +----+-----------------+------------+
    | 1  | taipei-city     | 台北市      |
    | 2  | kaoh-siung-city | 高雄市      |


