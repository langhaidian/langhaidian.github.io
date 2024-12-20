---
title: "debian 11基础环境准备"
date: 2024-06-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["debian 11"]
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

#### 1. 替换默认源
    cat << EOF > /etc/apt/sources.list 
    # Official sources.list
    deb http://deb.debian.org/debian bullseye main contrib non-free
    deb-src http://deb.debian.org/debian bullseye main contrib non-free

    deb http://deb.debian.org/debian bullseye-updates main contrib non-free
    deb-src http://deb.debian.org/debian bullseye-updates main contrib non-free

    deb http://deb.debian.org/debian bullseye-backports main contrib non-free
    deb-src http://deb.debian.org/debian bullseye-backports main contrib non-free

    deb http://security.debian.org/debian-security/ bullseye-security main contrib non-free
    deb-src http://security.debian.org/debian-security/ bullseye-security main contrib non-free
    EOF

    更新系统：
      sudo apt update #更新源
      sudo apt upgrade #更新已安装的包
      sudo apt dist-upgrade #升级系统
      sudo apt clean && sudo apt autoclean #清理下载文件的存档
#### 2. 给root账户设置或者修改密码

    passwd root

#### 3. 安装sudo
    apt install sudo

    **给普通用户添加sudo权限
    usermod -a -G sudo XXXX

#### 4. 默认root禁止ssh远程登录(推荐保持默认)

    修改配置文件
    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
    sudo vi /etc/ssh/sshd_config

    #Authentication:
    #LoginGraceTime 2m
    #PermitRootLogin prohibit-password
    #StrictModes yes
    #MaxAuthTries 6
    #MaxSessions 10
    #PubkeyAuthentication yes

    增加一句
    PermitRootLogin yes

    快速配置：
    sudo sed -i "/^#PermitRootLogin/c"PermitRootLogin" " yes"" /etc/ssh/sshd_config

    补充：
    PermitRootLogin prohibit-password  #允许 root 登录，但禁止适用密码认证
    可以配合使用 Pubkey 认证，修改 PubkeyAuthentication yes

    重启服务生效：
    sudo systemctl restart ssh

#### 5. 配置网络
    vim /etc/network/interfaces
    ============
    # 修改为以下内容：
    iface lo inet loopback
    auto lo

    auto eth0
    iface eth0 inet static
    address 10.10.1.5
    netmask 255.255.255.0
    #broadcast 10.10.1.255 #可选，上述配置了掩码
    #network 10.10.1.0 #可选，上述配置了掩码
    gateway 10.10.1.1
    #dns-domain sysin.org
    # domain 和 search 不能共存；如果同时存在，后面出现的将会被使用。
    dns-search sysin.org
    dns-nameservers 10.10.1.1 8.8.8.8 #注意 debian 11 默认没有安装 resolvconf，所以需要手动在 resolv.conf 中编辑 DNS
    ============
    # 或者安装 resolvconf 后，在这里配置，将自动更新 resolv.conf
    sudo apt install resolvconf
    # iface eth0 inet6 auto #ipv6 自动配置，不写则表示禁用了
    ============
    # DHCP 配置如下：
    auto eth0
    iface eth0 inet dhcp
    =============
    # 配置 DNS
    cat /etc/resolv.conf #如果已经安装 resolvconf，不要手动编辑，直接在上述 interfaces 中编辑 dns

    =============
    # 重启网络
    sudo systemctl restart networking
    #sudo service networking restart

    =================
    配置多个ip
    auto eth0:0
    iface eth0:0 inet static
        address 192.168.1.90
        netmask 255.255.255.0

    auto eth0:1
    iface eth0:1 inet static
        address 192.168.1.91
        netmask 255.255.255.0
        gateway 192.168.1.1
    =======

    #添加静态路由信息
    cat >> /etc/network/interfaces <<EOF
    # static routes
    up ip route add 10.10.12.0/24 via 10.10.1.1 dev eth0
    up ip route add 10.10.13.0/24 via 10.10.1.1 dev eth0
    up ip route add 10.10.14.0/24 via 10.10.1.1 dev eth0
    up ip route add 10.10.15.0/24 via 10.10.1.1 dev eth0
    up ip route add 10.10.16.0/24 via 10.10.1.1 dev eth0
    EOF

    #重启网络
    systemctl restart networking

    #验证
    ip route

#### 6. 修改主机名
    vim /etc/hostname
    hostnamectl set-hostname debian-01 --static

    vim /etc/hosts
    # 添加一行
    127.0.0.1  debian01.sysin.org debian01
    # 注意这里的格式，IP 后面先写 FQDN 再写主机名，与 CentOS 相同
    # 先写 FQDN 后写主机名，顺序反了不影响解析，但是'hostname -f'命令无法显示 FQDN，只能显示主机名

#### 7. 安装intel无线网卡
    查看网线设备信息
    lspci -nn
    安装wifi驱动包
    apt update && apt install firmware-iwlwifi
    重新加载wifi模块
    modprobe -r iwlwifi ; modprobe iwlwifi

#### 8. 修改默认ulimt参数

    cat << EOF >> /etc/security/limits.conf
    * soft nofile 65535
    * hard nofile 65535
    * soft nproc 65535
    * hard nproc 65535
    EOF


    cat << EOF > /etc/security/limits.d/20-nproc.conf
    *          soft    nproc     65535
    root       soft    nproc     unlimited
    EOF

#### 9. 安装openjdk8

    ##sudo apt install apt-transport-https ca-certificates wget dirmngr gnupg software-properties-common 

    wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -

    sudo add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/

    sudo apt-get update && sudo apt-get install adoptopenjdk-8-hotspot
    sudo apt update
    sudo apt install adoptopenjdk-8-hotspot

#### 10. 安装docker及docker-compose
    
    ## 卸载旧版本软件
    sudo apt-get remove docker docker-engine docker.io containerd runc

    ## 安装依赖 
    sudo apt-get update
    sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
    ## 导入gpg key
    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    ## 添加仓库
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    
    ## 开始安装
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin


#### 11. 设置时区

##### 通过timedatectl进行设置
    检查当前时区:

    timedatectl
                   Local time: Fri 2020-04-03 19:23:29 UTC
           Universal time: Fri 2020-04-03 19:23:29 UTC
                 RTC time: Fri 2020-04-03 19:23:29
                Time zone: UTC (UTC, +0000)
                .....
    
    ##更改时区
    timedatectl list-timezones ｜ grep Shanghai ##过滤上海时区

    sudo timedatectl set-timezone your_time_zone  ##更改时区
    sudo timedatectl set-timezone Asia/Shanghai ##设置上海时区

    ##查看和验证时区
    timedatectl  
                   Local time: Fri 2020-04-03 13:30:30 CST
           Universal time: Fri 2020-04-03 19:30:30 UTC
                 RTC time: Fri 2020-04-03 19:30:30
                Time zone: America/Monterrey (CST, -0600)
                .......

    date ## 查看具体时间

##### 通过软连接进行设置

较旧版本debian，没有timedatectl可以通过软连接进行设置，可以通过/etc/localtime文件符号链接到/usr/share/zoShanghai

    ##查看正确时区进行设置
    sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

    ##验证
    date
    Fri 03 Apr 2020 01:34:27 PM CST


#### sbin
    export PATH=/usr/loca/sbin:/usr/sbin:/sbin:$PATH


#### 12 时间同步
a. 同步另外一台时间

    date --set="$ssh root@host date"
b. 搭建ntp服务器

    ntpdate hostip
    或者
    sntp -P no -r hostip


#### 13 修改docker默认/var/lib/docker存储

    a. 停止docker服务
    sudo systemctl stop docker
    sudo systemctl status docker

    查看服务状态
    ps faux | grep -i docker

    b. 创建新路径
    mkdir /new/dir

    c.修改daemon.json文件
    {
        "data-root": "/new/dir"
    }

    c.同步
    sudo cp -axT /var/lib/dockr /new/dir
    或者
    sudo rsync -avxP /var/lib/docker /new/path/docker-data-root


    d. 重启服务
    sudo systemctl daemon-reload
    sudo systemctl start docker

    e. 测试服务状态
    sudo docker images


