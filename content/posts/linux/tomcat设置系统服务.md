---
title: "tomcat设置系统服务"
date: 2024-03-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["tomcat"]
categories: ["tomcat"]
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

1. 编辑系统服务
    ```
    vi /etc/systemd/system/tomcat.service

    [Unit]
    Description=Tomcat8540
    After=syslog.target network.target remote-fs.target nss-lookup.target

    [Service]
    Type=oneshot
    ExecStart=/usr/local/apache-tomcat-8.5.40/bin/startup.sh
    ExecStop=/usr/local/apache-tomcat-8.5.40/bin/shutdown.sh
    ExecReload=/bin/kill -s HUP $MAINPID
    RemainAfterExit=yes

    [Install]
    WantedBy=multi-user.target
    ```

2.  设置开机启动
    ```
    systemctl enable tomcat
    ```

3. 启动服务
    ```
    systemctl start tomcat
    ```


