---
title: "Dockerfile文件编写"
date: 2022-02-10T11:04:49+08:00
# weight: 1
# aliases: ["/first"]
tags: ["docker"]
categories: ["docker"]
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


![docker](/img/docker-logo.png)


## 情况说明：

- 公司业务最近正在转型中，从传统模式转变到微服务架构设计，最终落到docker的方式进行服务打包部署。后期随着规模扩大也有可能使用k8s方式进行业务扩充，直至SaaS方式提供专有服务。
- 研发部分目前感觉只专注于自己的业务开发，基础设施服务方面没有过多涉及，遂着手进行Dockerfile文件编写打包服务，项目以Spring Boot开发方式为主。

## 具体步骤:

### 1. 获取应用程序

结构如下

![运行效果](/img/Dockerfile.png)


### 2. dockerfile编写

    FROM openjdk:8-jdk-alpine  # 使用alpine版本作为基础镜像

    LABEL "maintainer"="sergio13848.com"   # 设置标签
    LABEL "rating"="chinadci" "class"="pipe2d3d"  # 设置标签

    RUN apk add --update tzdata && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
        && echo "Asia/Shanghai" > /etc/timezone \
        && apk del tzdata \
        && apk add --update --no-cache bash   # 安装tzdata设定时区


    VOLUME [ "/opt/2d3d" ]   # 设置挂载目录
    WORKDIR /data   # 设置工作目录

    COPY *.jar pipe2d3d.jar 
    COPY bootstrap.yml bootstrap.yml
    COPY run.sh .
    COPY wait .    #拷贝源码到工作目录下
    RUN chmod +x run.sh \
        && chmod +x wait   # 给予运行参数

    EXPOSE  29670  # 服务暴露端口号

    CMD ./wait && ./run.sh # 运行，wait为检测服务状态是否就绪，上个章节有讲


### 3. run.sh编写

使用运行脚本与docker封装脱离

    #!/bin/sh
    set -e
    exec java -Xms128M -Xmx512g \
        -jar -Duser.timezone=Asia/Shanghai \
        -Djava.security.egd=file:/dev/./urandom 
        pipe2d3d.jar

### 4. 编写docker-compose进行打包配置

    2d3d:
        build: ./2d3d/
        image: 2d3d:latest
        container_name: 2d3d
        restart: always
        ports:
            - 29670:29670
        volumes:
            - ./2d3d:/data
        environment:
            WAIT_HOSTS: chinadci-nacos:8848, postgres:5432 # 检测其他服务是否启动在启动本服务
        depends_on:
            - nacos
            - postgis

### 5. 配置成功效果

![打包运行效果](/img/docker-run.png)

### 6. 后续操作

- 后续可以搭建私有docker仓库存储打包镜像供使用。以harbor为例进行搭建。后续篇幅讲解以手动方式进行搭建。
