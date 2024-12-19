---
title: "sed简单介绍"
date: 2022-02-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["sed"]
categories: ["linux"]
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

#### 语法

    sed options...[script][inputfile]

## 第一部分

#### 准备测试数据

    cat <<EOF > sedtest.conf
    unix is great os. unix is opensource. unix is free os.
    learn operating system.
    unix linux which one you choose.
    unix is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.
    EOF

#### 1. 替换每行第一个字符

    $ sed 's/unix/linux/' sedtest.conf
    
    Output :
    linux is great os. unix is opensource. unix is free os.
    learn operating system.
    linux linux which one you choose.
    linux is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.

#### 2. 替换每行第二个字符

    $ sed 's/unix/linux/2' sedtest.conf

    Output :
    unix is great os. linux is opensource. unix is free os.
    learn operating system.
    unix linux which one you choose.
    unix is easy to learn.linux is a multiuser os.Learn unix .unix is a powerful.

#### 3. 替换每行所有字符

    $ sed 's/unix/linux/g' sedtest.conf

    Output :
    linux is great os. linux is opensource. linux is free os.
    learn operating system.
    linux linux which one you choose.
    linux is easy to learn.linux is a multiuser os.Learn linux .linux is a powerful.

#### 4. 替换每行第三个开始的字符

    $ sed 's/unix/linux/3g' sedtest.conf

    Output :
    unix is great os. unix is opensource. linux is free os.
    learn operating system.
    unix linux which one you choose.
    unix is easy to learn.unix is a multiuser os.Learn linux .linux is a powerful.

#### 5. 字符分割加符号

    $ echo "Welcome To The Geek Stuff" | sed 's/\(\b[A-Z]\)/\(\1\)/g'

    Output :
    (W)elcome (T)o (T)he (G)eek (S)tuff

#### 6. 替换第几行的第一个字符(第三行)

    $ sed '3 s/unix/linux' sedtest.conf

    Output :
    unix is great os. unix is opensource. unix is free os.
    learn operating system.
    linux linux which one you choose.
    unix is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.

#### 7. 显示被替换的行内容(被替换的显示双行，没有替换的显示原始数据)

    $ sed 's/unix/linux/p' sedtest.conf

    Output :
    linux is great os. unix is opensource. unix is free os.
    linux is great os. unix is opensource. unix is free os.
    learn operating system.
    linux linux which one you choose.
    linux linux which one you choose.
    linux is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.
    linux is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.

#### 8. 只显示被替换的内容

    $ sed -n 's/unix/linux/p' sedtest.conf

    Output :
    linux is great os. unix is opensource. unix is free os.
    linux linux which one you choose.
    linux is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.

#### 9. 替换给定行数字符

    $ sed '1,3 s/unix/linux/' sedtest.conf

    Output :
    linux is great os. unix is opensource. unix is free os.
    learn operating system.
    linux linux which one you choose.
    unix is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful.

    $ sed '2,$ s/unix/linux/' geekfile.txt

    Output :
    unix is great os. unix is opensource. unix is free os.
    learn operating system.
    linux linux which one you choose.
    linux is easy to learn.unix is a multiuser os.Learn unix .unix is a powerful

    *** $ 代表文件最后一行内容 ***

#### 10. 删除行内容

    语法：$ sed 'nd' filename.txt
    例子：$ sed '5d' filename.txt

    $ sed '$d' filename.txt  //删除最后一行内容

    $ sed 'x,yd' filename.txt //删除x到y之间行内容
      sed '3,6d' filename.txt

    $ sed 'nth,$d' filename.txt //  删除第几行到最后一行
      sed '12,$d' filename.txt
    
    $ sed '/pattern/d' filename.txt //删除匹配到行
      sed '/abc/d' filename.txt

### 第二部分

#### 准备测试数据

    cat <<EOF > sedtest.txt
    life isn't meant to be easy, life is meant to be lived.
    Try to learn & understand something new everyday in life.
    Respect everyone & most important love everyone.
    Don’t hesitate to ask for love & don’t hesitate to show love too.
    Life is too short to be shy.
    In life, experience will help you differentiating right from wrong.
    EOF

### 文件间距处理

#### 1. 每行最后插入一个空白行

    $ sed G sedtest.txt

    Output: 
    life isn't meant to be easy, life is meant to be lived.

    Try to learn & understand something new everyday in life.

    Respect everyone & most important love everyone.

    Don’t hesitate to ask for love & don’t hesitate to show love too.

    Life is too short to be shy.

    In life, experience will help you differentiating right from wrong.

#### 2. 插入2行空白行

    sed 'G;G' sedtest.txt

#### 3. 删除空白行，在每行后面插入空白行

    sed '/^$/d;G' sedtest.txt

#### 4. 在匹配行上面插入一条黑线(匹配love)

    sed '/love/{x;p;x;}' sedtest.txt

#### 5. 在匹配行下面插入空白行(匹配love)

    sed '/love/G' sedtest.txt

#### 6. 在每行左边插入5个空格

    sed 's/^/     /' sedtest.txt

### 编号处理

#### 1. 每一行进行编号(左对齐)，\t为数字跟语句之间的制表符

    sed = a.txt | sed 'N;s/\n/\t'

#### 2. 每行插入编号(数字左边，右对齐)，类似于cat -n filename 增加编号效果

    sed '/./=' a.txt | sed 'N;s/^/    ;s/*\(.\{4,\}\)\n/\1 /'

#### 3. 不为空行进行编号

    sed '/./=' a.txt | sed '/./N; s/\n/ /'

### 删除行

#### 1. 删除指定行数

    sed '5d' a.txt   //删除第五行

#### 2. 删除最后一行

    sed '$d' a.txt    //$ 代表最后一行

#### 3. 删除指定范围行数内容

    sed '3,5d' a.txt   //删除第3-5行内容 

#### 4.删除置指定行数到最后一行

    sed '2,$d' a.txt   //第二行到最后一行

#### 5. 删除匹配内容行内容

    sed '/life/d' a.txt

#### 6. 删除从第n行开始的2行内容

    sed '3~2d' a.txt

#### 7. 删除匹配内容及其后的两行

    sed '/easy/,+2d' a.txt

#### 8. 删除空白行

    sed '^$/d' a.txt

#### 9. 删除空白行及#开始内容

    sed -i '/^#/d;/^$/d' a.txt

### 查看打印文件

#### 1. 查看指定范围行数内容

    sed -n '2,5p' a.txt

#### 2. 查看指定范围外内容

    sed '2,4d' a.txt

#### 3. 查看文件第几行

    sed -n '4'p a.txt  //第4行内容

#### 4. 查看指定范围内内容

    sed -n '4,6'p a.txt

#### 5. 查看最后一行内容

    sed -n '$'p a.txt

#### 6. 查看指定行术到最后一行内容

    sed -n '3,$'p a.txt //第三行到最后一行

#### 7 打印匹配内容行内容

    sed -n /every/p a.txt

#### 8. 打印匹配内容到到第n行

    sed -n '/everyone/,5p' a.txt  //从匹配行打印到第5行

#### 9. 打印从第x行开始直到匹配的行内容

    sed -n ‘x,/pattern/p’ filename 
    sed -n '1,/everyone/p' a.txt

#### 10. 打印匹配的行直到第x行

    sed -n '/pattern/,+xp' filename
    sde -n '/learn/,+2p' a.txt

### 替换命令

#### 1. 替换第一次出现的内容

    sed 's/life/leaves/' a.txt

#### 2. 替换一行中第x次出现内容

    sed 's/to/two/2' a.txt

#### 3. 替换所有内容

    sed 's/life/learn/g' a.txt

#### 4. 替换从第x开始的字符

    sed 's/to/TWO/2g' a.txt

#### 5. 替换第x行内容

    sed '3 s/every/each/' a.txt
    sed '3 s/every/each/p' a.txt //打印 

#### 6. 替换指定范围内行内容

    sed '2,5 s/to/TWO/' a.txt

#### 7. 替换内容忽略大小写

    sed 's/life/Love/i' a.txt
    sed 's/[Ll]ife/Love/g' a.txt  //匹配life、Life替换内容

#### 8. 使用一个空格替换所有

    sed 's/  */ /g' filename

#### 9. 使用一种匹配模式替换另一个内容

    Syntax: sed ‘/followed_pattern/ s/old_pattern/new_pattern/’ filename 
    sed '/is/ s/live/love' a.txt  //匹配第一个内容去替换

#### 10. 除过第x行

    sed -i '5!s/life/love' a.txt   //第5行除过