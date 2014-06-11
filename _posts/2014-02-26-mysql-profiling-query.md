---
layout: post
title:  測量mysql query的效能
date:   2014-02-26 09:25:17
categories:
tags: mysql
---

最近針對公司網站的API，在資料庫的query上做最佳化的處理。除了把一些n+1的query去除之外，也希望可以針對單一query上的語法進行最佳化。不過要能看到改善的幅度，還是要有個測量的標準，幸好mysql內建就有profiling與explain的機制可以使用。這邊來記錄分享一下。

# Explain

改善mysql效能很重要的一點就是避免full-table scan，也就是為了找一筆資料，卻必須要掃整個table，會有這種問題通常都是index沒下好。explain這個指令原本是用在查詢table的欄位資訊，不過在select的時候加上explain這個指令則可以列出一個query會動到的資料數，同時也會分析query所用到的table與index，幫助我們更容易找出適合使用index的column。

使用的方式很簡單，只要在想要測試的query前加上explain就可以了：

    mysql> explain select * from users where id = 1234;
    +----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
    | id | select_type | table | type  | possible_keys | key     | key_len | ref   | rows | Extra |
    +----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
    |  1 | SIMPLE      | users | const | PRIMARY       | PRIMARY | 4       | const |    1 |       |
    +----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
    1 row in set (0.00 sec)

上面的例子因為我們用的是primary key id來查詢，所以rows的個數是1，也就是只需要動到一筆資料的查詢。如果換成下面這個例子那就可怕了：

    mysql> explain select * from users where email like "%user%" limit 10000;
    +----+-------------+-------+------+---------------+------+---------+------+---------+-------------+
    | id | select_type | table | type | possible_keys | key  | key_len | ref  | rows    | Extra       |
    +----+-------------+-------+------+---------------+------+---------+------+---------+-------------+
    |  1 | SIMPLE      | users | ALL  | NULL          | NULL | NULL    | NULL | 4918218 | Using where |
    +----+-------------+-------+------+---------------+------+---------+------+---------+-------------+
    1 row in set (0.00 sec)

explain尤其在join等複雜的query中會特別好用，它會列出所有會動到的table與資料數，對應使用的index也會列出來：

    mysql> explain select * from users A, purchases B where A.id = B.user_id;
    +----+-------------+-------+--------+----------------------------+---------+---------+----------------------+--------+-------+
    | id | select_type | table | type   | possible_keys              | key     | key_len | ref                  | rows   | Extra |
    +----+-------------+-------+--------+----------------------------+---------+---------+----------------------+--------+-------+
    |  1 | SIMPLE      | B     | ALL    | index_purchases_on_user_id | NULL    | NULL    | NULL                 | 122266 |       |
    |  1 | SIMPLE      | A     | eq_ref | PRIMARY                    | PRIMARY | 4       | db_example.B.user_id |      1 |       |
    +----+-------------+-------+--------+----------------------------+---------+---------+----------------------+--------+-------+
    2 rows in set (0.00 sec)

更多explain顯示的欄位與內容可以參考[MySQL 5.0 Reference Manual - 8.2.2 EXPLAIN Output Format][doc-explain]

# Profiling

如果想要知道實際上query花了多久的時間，就要使用profiling的功能了。使用的方式也很簡單，首先使用mysql client連到資料庫後，先設定`profiling`的值為1：

    mysql> set profiling=1;

設定之後，它就會打開profiling的功能，讓這次的client session的接下來每一個query都會進行profiling。接著就執行你要的query，例如：

    mysql> select * from users limit 10000;

要看profiling的結果可以用`show profiles`：

    mysql> show profiles;
    +----------+------------+---------------------------------+
    | Query_ID | Duration   | Query                           |
    +----------+------------+---------------------------------+
    |        1 | 0.25381300 | select * from users limit 10000 |
    +----------+------------+---------------------------------+
    2 rows in set (0.00 sec)

它會顯示每一筆query所花的時間。如果想看每一筆query更詳細的時間資訊，則可以用`show profile`的方式帶Query ID來查詢：

    mysql> show profile for query 1;
    +----------------------+----------+
    | Status               | Duration |
    +----------------------+----------+
    | starting             | 0.000113 |
    | checking permissions | 0.000012 |
    | Opening tables       | 0.000089 |
    | System lock          | 0.000019 |
    | init                 | 0.000043 |
    | optimizing           | 0.000012 |
    | statistics           | 0.000022 |
    | preparing            | 0.000017 |
    | executing            | 0.000005 |
    | Sending data         | 0.252374 |
    | end                  | 0.000041 |
    | query end            | 0.000009 |
    | closing tables       | 0.000013 |
    | freeing items        | 0.001035 |
    | logging slow query   | 0.000006 |
    | cleaning up          | 0.000006 |
    +----------------------+----------+
    16 rows in set (0.00 sec)

除了時間資訊，還可以查其它例如cpu time, block IO等資訊(更多說明請參考：[MySQL 5.1 Reference Manual - 13.7.5.32 SHOW PROFILE Syntax][doc-show-profile])：

    mysql> show profile cpu for query 1;
    +----------------------+----------+----------+------------+
    | Status               | Duration | CPU_user | CPU_system |
    +----------------------+----------+----------+------------+
    | starting             | 0.000113 | 0.000000 |   0.000000 |
    | checking permissions | 0.000012 | 0.000000 |   0.000000 |
    | Opening tables       | 0.000089 | 0.000000 |   0.000000 |
    | System lock          | 0.000019 | 0.000000 |   0.000000 |
    | init                 | 0.000043 | 0.000000 |   0.000000 |
    | optimizing           | 0.000012 | 0.000000 |   0.000000 |
    | statistics           | 0.000022 | 0.000000 |   0.000000 |
    | preparing            | 0.000017 | 0.000000 |   0.000000 |
    | executing            | 0.000005 | 0.000000 |   0.000000 |
    | Sending data         | 0.252374 | 0.059991 |   0.005999 |
    | end                  | 0.000041 | 0.000000 |   0.000000 |
    | query end            | 0.000009 | 0.000000 |   0.000000 |
    | closing tables       | 0.000013 | 0.000000 |   0.000000 |
    | freeing items        | 0.001035 | 0.000000 |   0.000000 |
    | logging slow query   | 0.000006 | 0.000000 |   0.000000 |
    | cleaning up          | 0.000006 | 0.000000 |   0.000000 |
    +----------------------+----------+----------+------------+
    16 rows in set (0.00 sec)

    mysql> show profile block io for query 1;
    +----------------------+----------+--------------+---------------+
    | Status               | Duration | Block_ops_in | Block_ops_out |
    +----------------------+----------+--------------+---------------+
    | starting             | 0.000113 |            0 |             0 |
    | checking permissions | 0.000012 |            0 |             0 |
    | Opening tables       | 0.000089 |            0 |             0 |
    | System lock          | 0.000019 |            0 |             0 |
    | init                 | 0.000043 |            0 |             0 |
    | optimizing           | 0.000012 |            0 |             0 |
    | statistics           | 0.000022 |            0 |             0 |
    | preparing            | 0.000017 |            0 |             0 |
    | executing            | 0.000005 |            0 |             0 |
    | Sending data         | 0.252374 |          696 |             0 |
    | end                  | 0.000041 |            0 |             0 |
    | query end            | 0.000009 |            0 |             0 |
    | closing tables       | 0.000013 |            0 |             0 |
    | freeing items        | 0.001035 |            0 |             0 |
    | logging slow query   | 0.000006 |            0 |             0 |
    | cleaning up          | 0.000006 |            0 |             0 |
    +----------------------+----------+--------------+---------------+
    16 rows in set (0.00 sec)

最後如果離開mysql client，profiling的動作就會結束，或是也可以手動設定`profiling=0`來關閉profiling的功能。

    mysql> set profiling=0;

# Refs:

[doc-show-profile]: https://dev.mysql.com/doc/refman/5.5/en/show-profile.html
[doc-explain]: https://dev.mysql.com/doc/refman/5.0/en/explain-output.html 
