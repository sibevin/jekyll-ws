---
layout: post
title:  mysql指令一覽
date:   2014-03-04 16:23:26
categories:
tags:   mysql cheat-sheet
---

# 資料庫

列出所有的database

    show databases;

列出[database]資料庫所有的table

    show tables from [database];

使用[database]資料庫，會讀取資料庫內所有table內的欄位資訊

    use [database];

刪除[database]資料庫

    drop database [database];

# 表格

列出某個table內欄位資訊

    desc [table];

刪除[table]內所有的資料，id從1開始計算(如果使用delete刪除所有的資料，則id不會從1開始)

    truncate [table];

刪除在[table]的某一欄[column]

    alter table [table] drop column [column];

修改[table]的預設編碼為[encoding]

    alter table [table] convert to character set [encoding];

# 選取

列出[table]內符合[condition]的資料

    select * from [table] where [condition];

列出[table]內符合[condition]的資料，只選取[col1]與[col2]

    select [col1], [col2] from [table] where [condition];

列出[table]內符合[condition]的資料，以[col]升幕排序

    select * from [table] where [condition] order by [col];

列出[table]內符合[condition]的前[count]筆資料

    select * from [table] where [condition] limit [count];

# 更新

更新[table]內符合[condition]的資料，將它的[col]值更新成[value]

    update [table] set [col]=[value] where [condition];

# 插入

插入一筆新資料到[table]中，其中[col1]的值為[value1]，[col2]的值為[value2]

    insert into [table] ([col1],[col2]) values ([value1],[value2]);

# 刪除

刪除所有的資料在[table]內符合[condition]的資料

    delete from [table] where [condition];

# 其它

查看mysql狀態

    status;
