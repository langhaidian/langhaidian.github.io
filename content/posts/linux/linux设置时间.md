---
title: "linux设置时间"
date: 2024-08-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: [“date"]
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

![centos7](/img/install-centos-7-logo.png)

* 最近碰到搭建测试k8s环境，系统版本太低导致很多问题，进行google后整理的升级步骤，供参考。

#### 1. 系统时间查看和设置

    查看系统时间的命令： #date
    设置系统时间的命令： #date +"月/日/年 时:分:秒"

    [root@localhost sbin]# date +"12/13/2021 12:13:14"
    12/13/2021 12:13:14
    [root@localhost sbin]# date
    Sat May 29 18:02:37 CST 2021


#### 2. 设置硬件时间

    查看硬件时间的命令： # hwclock
    设置硬件时间的命令： # hwclock --set --date="月/日/年 时:分:秒"

    [root@localhost sbin]# hwclock --set --date="2/1/2021 1:2:3"
    [root@localhost sbin]# hwclock
    Mon 01 Feb 2021 01:02:09 AM CST  -0.848901 seconds

    将系统时间写入硬件时间。hwclock --systohc