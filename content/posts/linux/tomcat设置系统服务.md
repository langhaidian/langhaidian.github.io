---
weight: 5
title: "tomcat设置系统服务"
date: 2022-02-10T11:04:49+08:00 
lastmod: 2022-02-10T11:04:49+08:00
draft: false
author: "langhaidian"
authorLink: "https://langhaidian.io"
description: "tomcat设置系统服务"
resources:
- name: "featured-image"
  src: "/img/install-centos-7-logo.png"

tags: ["tomcat"]
categories: ["linux"]

lightgallery: true
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


