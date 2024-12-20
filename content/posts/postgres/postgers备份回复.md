---
title: "postgresql简单记录备份"
date: 2024-02-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["virtualenv python"]
categories: ["postgresql"]
author: "langhaidian"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "" # edit text
    appendFilePath: true # to append file path to Edit link
---


### 备份
 pg_dump -F c -F 路径/testdb.dmp -h 127.0.0.1 -U postgres testdb

### 恢复
 pg_restore -d testdb testdb.dmp -h 127.0.0.1 -U postgres

 ### 数据库归档模式：

```
1. 修改postgresql.conf文件中的相关参数，包括：
archive_mode = on # 开启归档模式，可以是off, on, 或者always
archive_command = ‘DATE=`date +%Y%m%d`;DIR=/var/lib/postgresql/arch/$DATE;test ! -d $DIR && mkdir -p $DIR;test ! -f $DIR/%f && cp %p $DIR/%f’ # 指定归档命令，可以是一个shell命令或者脚本，其中%p表示WAL文件的完整路径，%f表示WAL文件的名称
wal_level = replica # 或者logical，指定WAL日志的输出级别，minimal不支持归档
2. 创建归档目录，并赋予postgres用户相应的权限，例如：
mkdir -p /var/lib/postgresql/arch
chown -R postgres.postgres /var/lib/postgresql/arch/
3. 重启PostgreSQL服务，使配置生效，例如：
service postgresql-13 restart
4. 验证归档模式是否开启成功，可以通过以下方法：
查看archive_mode参数的值，应该是on或者always
手动切换WAL日志文件，使用select pg_switch_wal()命令，并查看归档目录是否有新的WAL文件生成
查看日志文件中是否有归档相关的信息或者错误
```

### 备份脚本
```
#!/bin/bash

BACKUP_LOCAL_DIR=/opt/local
BACKUP_CON_DIR=/opt/con

BACKUP_DAY=7

#数据库名称
DB_NAME_dev=dev_dotnet
TODAY=`data +%Y-%m-%d`

#删除超过7天的备份
find $BACKUP_LOCAL_DIR -type f -name dev_dotent* -mtime +BACKUP_DAY -exec rm -rf {} \;

#进行数据备份
sudo docker exec -it postgres pg_dump -F c -f $BACKUP_CON_DIR/dev_dotnet_$TODAY.dmp -h 127.0.0.1 -U postgres dev_dotnet

#压缩备份文件
sudo gzip $BACKUP_LOCAL_DIR/dev_dotnet_$TODAY.dmp

#显示备份成功
echo "$DB_NAME_dev $TODAY 数据库备份成功！"

#归档日志处理
vi postgresql.conf

archive_mode = on
archive_command = 'DATE=`date +%Y%m%d`; DIR="/var/lib/postgresql/arch/$DATE"; test ! -d $DIR && mkdir -p $DIR; test ! -f $DIR/%f && cp %p %DIR/%f; find  /var/lib/postgresql/arch/ -type d -mtime 7 -exec rm -f {} \;'
```