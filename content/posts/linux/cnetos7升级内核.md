---
title: "centos7内核4.19升级到5.14"
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

* 最近碰到搭建测试k8s环境，系统版本太低导致很多问题，进行google后整理的升级步骤，供参考。

#### 1. 查看内核版本

    uname -msr
#### 2. 更新软件版本

    sudo yum -y update

#### 3. 导入eplr

    rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

#### 4. ELRepo

    yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm

#### 5. 查找现有内核版本

    yum list available --disablerepo='*' --enablerepo=elrepo-kernel
    * ML为主线版本，LT为长期支持版本

#### 6. 安装内核版本

    sudo yum --enablerepo=elrepo-kernel install kernel-lt

#### 7. 删除旧版本tools

    yum remove kernel-tools-libs.x86_64 kernel.x86_64  -y
    yum remove kernel-tools-libs.x86_64 kernel-tools.x86_64  -y

#### 8. 安装工具包

    yum --disablerepo=\* --enablerepo=elrepo-kernel install kernel-lt-tools.x86_64  -y

#### 9. 重启手动选择内核进入系统检查系统情况

    reboot

#### 10. 查看所有启动内核

     awk -F \' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg 

     修改默认启动内核：grub2-set-default 0

#### 11. 编辑启动顺序

    sudo vim /etc/default/grub
    ---------------------
    * Once the file opens, look for the line that says 
    GRUB_DEFAULT=X, and change it to GRUB_DEFAULT=0 (zero). 
    * This line will instruct the boot loader to default
     to the first kernel on the list, which is the latest.

    将GRUB_DEFAULT=X 修改成GRUB_DEFAULT=0   加载第一个内核版本并且为最后一个版本

#### 12. 更新启动配置文件

    sudo grub2-mkconfig -o /boot/grub2/grub.cfg

#### 13. 查看启动顺序

    grub2-editenv list

#### 14. 重启验证

    reboot
