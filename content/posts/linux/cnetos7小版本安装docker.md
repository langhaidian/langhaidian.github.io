---
title: "centos7离线安装docker"
date: 2022-02-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["centos"]
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

* 内网环境centos 7.2需要安装docker，并运行相关系统

#### 1. 查看内核版本

    uname -msr

#### 2. 更新软件版本

docker run --rm -v ${PWD}/bin:/tmp -it centos:7.6.1810 bash
# In the online container:
cd /tmp
yum-config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo 
yum install -y yum-utils
yum makecache fast 
yum list docker-ce --showduplicates | sort -r 
yumdownloader --resolve docker-ce-18.06.3.ce-3.el7 docker-ce-cli-24.0.7-1.el7 containerd.io-1.6.27-3.1.el7 docker-buildx-plugin-0.11.2-1.el7 docker-compose-plugin-2.21.0-1.el7 yum-utils

rpm -ivh --replacefiles --replacepkgs *.rpm
rpm -ivh ./openssh74pl/*.rpm --force --nodeps


docker run --rm -v ${PWD}/bin:/tmp -it debian:11.7 bash

默认存放下载的deb文件的路径是
/var/cache/apt/archives

apt install --download-only xxxx

比如要下载tree这个deb包, 则使用如下指令：
apt download tree


离线安装deb包
sudo dpkg -i <package.deb>