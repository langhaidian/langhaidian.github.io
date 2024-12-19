---
title: "geoscen server基础部署-centos 7.6"
date: 2022-02-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["geoscene"]
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

#### 1. 准备工作
1.1 关闭防火墙

    停止防火墙：systemctl stop firewalld.service
    禁用防火墙的开机启动：systemctl disable firewalld.service
    查看防火墙状态：systemctl status firewalld.service

1.2 用户和组创建

    # groupadd xcy
    # useradd -g xcy -m geoscene   //绑定用户和用户组
    # passwd geoscene    //设置密码geoscene

1.3 修改计算机名：
    
    # hostname  //查看计算机名
    # vi /etc/hostname
    编辑机器名为xcytest后保存退出

    # vi /etc/sysconfig/network
    文件中的hostname修改为xcytest

1.4 修改hosts文件

    # vi /etc/hosts
    192.168.0.130 xcytest.portal.com xcytest  //增加配置信息

1.5 检查主机名是否生效

    # hostname xcytest   //让修改立即生效
    # hostname -f  //查看完全限定域名
      xcytest.portal.com

1.6 检查磁盘大小情况

    df -h
    确保/home/径路下存储空间满足要求
    
1.7 准备安装包
    安装包：geoscene_datastore、geoscene_portal、geoscne_server、geoscene_webadapter_java
    将安装包上传到/home/geoscen/azb

    解压安装包： tar -zxvf 包名称

1.8 赋予权限

    mv geoscene_datastore_20220130 geoscenedatastore  //修改软件名称
    ......       //修改软件名称，每个都需要修改，非必需


    chown -R geoscene:xcy geoscenserver  //绑定用户
    chown -R 755 geoscenserver     //给予权限，每个安装包都要执行

#### 2. 安装配置
2.1 安装前准备
    
    修改文件打开数配置
    # vi /etc/security/limits.conf //添加如下内容
    geoscene soft nofile 65535
    geoscene hard nofile 65535
    geoscene soft nproc 65535
    geoscene hard nporc 65535

    # ulimit -Hn -Hu  //使修改生效
    # ulimit -Sn -Su  //使修改生效

2.2 诊断当前环境是否满足安装要求

    # su geoscene //切换到geoscene账户下
    # ./geosceneserver/setup_resources/serverdiag/serverdiag
    ........
    ........   
    //根据提示检查是否有问题进行修改

2.3 安装geoscene server

    A：
    利用console模式进行交互安装
    # cd geosceneserver
    # ./Setup -m console
    按照提示进行选择，以及配置授权文件。记录网站登录地址。

    B：
    创建server站点
    复制2.3安装完成后的地址：
    1. 创建站点
    2. 设置管理员账户的用户名和密码（siteadmin、geoscene、geoscene）
    3. 设置根服务器目录和配置存储位置(此两个目录用户有足够的权限及存储空间要大，可以推荐/home路径)
    4. 点击完成，直至安装成功。记录账户密码

2.4 安装和配置geoscene datastore

    A:
    设置vm.swappiness和vm.max_map_count，转至root账户下，设置值以满足时空大数据分析需要
    # sysctl -w vm.map_count=262144
    # sysctl -w vm.swappiness=1

    B:
    诊断当前环境是否满足datastore安装要求
    # su - geoscene
    # ./geoscenedatastore/setup-resource/datastorediag/datastorediag
    按照提示确认环境是否满足，有问题进行修正，再次检查

    C:
    利用silent模型进行安装
    # cd geoscenedatastore
    # ./Setup =m silent
    记录安装之后的安装路径及地址信息

    D:
    登录上步安装之后登录地址，开始配置geoscene
    1. 主站点地址、账户、密码（6443主网站、siteadmin、geoscene、geoscene）
    2. 设置内容目录位置
    
    E:
    配置数据类型、按照硬件环境进行选择，如果正式服务器三个全选，虚拟机建议时空可以不选
    记录配置信息，点击完成安装

2.5 安装和配置portal for geoscene

    A:
    安装包文件，转至root账户下：
    # yum install dos2unix

    B:
    安装portal
    # ./Setup -m console
    记录安装之后的地址

    C:
    登录上步安装之后地址信息
    1. 创建新门户
    2. 选择准备好的portal授权文件(json文件)
    3. 设置初始管理员账户（portaladmin、geoscene、geoscene、Creator、其他信息）
    4. 配置站点内容目录（/home/geoscene/portal/usr/arcgisportal/content）
    5. 确认站点信息、点击完成

2.6 安装和配置web adapter

    准备jdk-1.8、tomcat-8、linux下安装包

#### 3. 配置防火墙

3.1 geoscene server 自启动[注意路径安装目录]

    复制安装文件到 /etc/systemd/system下
    cp /home/geoscene/geoscene/server/framework/etc/scripts/geosceneserver.service  /etc/systemd/system

    修改权限 600
    chmod 600 geosceneserver.service

    创建软连接
    ln -s ../geosceneserver.service /etc/systemd/system/multi-user.target.wants/geosceneserver.service

3.2 geoscene datastore [注意路径安装目录]

    复制安装文件到 /etc/systemd/system下
    cp /home/geoscene/geoscene/datastore/framework/etc/scripts/geoscenedatastore.service  
    /etc/systemd/system

    修改权限 600
    chmod 600 geoscenedatastore.service
    
    创建软连接
    ln -s ../geoscenedatastore.service /etc/systemd/system/multi-user.target.wants/geoscenedatastore.service

3.4 添加成功后查看对应添加的服务

    停止服务
    systemctl stop arcgisserver.service  
    
    启动服务
    systemctl start arcgisserver.service
    
    状态查看
    systemctl status arcgisserver.service