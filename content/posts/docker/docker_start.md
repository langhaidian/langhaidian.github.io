---
title: "docker-compose下容器启动顺序控制"
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
    Text: "提交修改" # edit text
    appendFilePath: true # to append file path to Edit link
---

![docker](/img/docker-logo.png)

## 情况说明：

- docker-compose中检测服务启动后的状态，也是最近碰到了业务中需要控制服务状态启动顺序，翻阅官方、google和群友帮助研究出此文。
- 不同于depends_on此种方式只是启动顺序配置，不管服务状态是否启动，要检测服务中的状态还需要借助第三方脚本或者其他程序，官方有推荐wait-for-it, dockerize,wait-for, RelayAndContainers等方式。
- 本文件使用[wait](https://github.com/ufoscout/docker-compose-wait) 提供的程序进行检测和启动,github地址为：https://github.com/ufoscout/docker-compose-wait；也是可以参考官方使用方法，rust编写的二进制文件无依赖，直接执行。

## 具体步骤：

### 1. 下载程序
        (通过离线方式进行下载封装，具体原因不解释。)
        wget -O https://github.com/ufoscout/docker-compose-wait/releases/download/2.9.0/wait 

### 2. dockerfile封装

        FROM openjdk:8-jdk-alpine

        LABEL "maintainer"="sergio13848.com"
        LABEL "rating"="chinadci" "class"="pipe2d3d"

        ENV JVM_XMS="1g" \
            JVM_XMX="1g" \
            JVM_XMN="512m" \
            JVM_MS="128m" \
            JVM_MMS="320m"

        RUN apk add --update tzdata && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
            && echo "Asia/Shanghai" > /etc/timezone \
            && apk del tzdata \
            && apk add --update --no-cache bash


        VOLUME [ "/opt/2d3d" ]
        WORKDIR /data

        COPY *.jar pipe2d3d.jar
        COPY bootstrap.yml bootstrap.yml
        COPY run.sh .
        COPY wait .     # 拷贝wait程序至容器
        RUN chmod +x run.sh \
            && chmod +x wait   # 赋予执行权限


        EXPOSE  29670

        CMD ./wait && ./run.sh


### 3. 修改docker-compose配置方式

        nacos:
            image: nacos/nacos-server:2.0.2
            container_name: nacos
            privileged: true
            restart: always
            env_file:
                - ./env/nacos-standlone-mysql.env
            volumes:
                - ./nacos/standalone-logs/:/home/nacos/logs
            ports:
                - 8848:8848
                - 9848:9848
                - 9555:9555
            depends_on:
                - mysql

        -------
        2d3d:
            build: ./2d3d/
            image: chinadci/2d3d:latest
            container_name: chinadci-2d3d
            privileged: true
            restart: always
            ports:
                - 29670:29670
            volumes:
                - ./2d3d:/data
            environment:
                WAIT_HOSTS: nacos:8848  # 此处配置需要等待的服务(为容器启动后的服务器名称和端口号)，
                还有启动参数可以参考github介绍
            depends_on:
                - nacos
### 4. 配置成功效果

![运行效果](/img/docker-run.png)

### 5 打包所有docker镜像
docker images --format "{{.Repository}}:{{.Tag}}" | xargs docker save | gzip > all-images.tar.gz