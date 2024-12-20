---
title: "iperf测试带宽"
date: 2024-07-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["iperf"]
categories: ["network"]
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

* 有项目需求需要测试局域网内带宽，网上整理后输出。

1. 设置服务器端
    iperf -s -w 2m
    缓存区2m单向测试，-s代表服务器端

2. 设置客户端
    iperf -c  serverip -w 2m -t 30s -i 1s
    缓存区2m单向测试，运行时间30秒，每秒显示一次结果

    iperf -c serverip -u -b 500M -w 2m -t 30s -i 1s
    测试500m带宽，使用udp协议


### 常用选项：

```

-f, --format
[kmKM] 报告格式：Kbits、Mbits、KBytes、MBytes

-h, --help
打印帮助大纲

-i, --interval n
在定期带宽报告之间暂停 n 秒

-l, --len n[KM]
将读/写缓冲区长度设置为 n（默认为 8 KB）

-m, --print_mss
打印 TCP 最大数据段长度（MTU - TCP/IP 标头）

-o, --output <filename>
将报告或错误消息输出到此指定文件

-p, --port n
将要侦听/连接的服务器端口设置为 n（默认为 5001）

-u, --udp
使用 UDP 而不是 TCP

-w, --window n[KM]
TCP 窗口大小（套接字缓冲区大小）

-B, --bind <host>
绑定到 <host>，接口或多播地址

-C, --compatibility
用于较低版本，不发送额外消息

-M, --mss n
设置 TCP 最大数据段长度（MTU - 40 字节）

-N, --nodelay
设置 TCP no delay，禁用 Nagle 算法

-v, --version
打印版本信息并退出

-V, --IPv6Version
将域设置为 IPv6

-x, --reportexclude
[CDMSV] 排除 C(connection) D(data) M(multicast) S(settings)
V(server) 报告

-y, --reportstyle C|c
是否设置 C 或 c 报告结果为 CSV（逗号分隔值）

特定于服务器的选项：  

-s, --server
在服务器模式下运行

-U, --single_udp
在单线程 UDP 模式下运行

-D, --daemon
将服务器作为守护程序运行

特定于客户端的选项：

-b, --bandwidth n[KM]
将目标带宽设置为 n 位/秒（默认为 1 Mbit/秒）。此
设置需要 UDP (-u)。

-c, --client <host>
在客户端模式下运行，连接到 <host>

-d, --dualtest
同时执行双向测试

-n, --num n[KM]
要传输的字节数（而不是 -t）

-r, --tradeoff
单独执行双向测试

-t, --time n
传输时间，以秒为单位（默认为 10 秒）

-F, --fileinput <name>
从文件输入要传输的数据

-I, --stdin
从 stdin 输入要传输的数据

-L, --listenport n
接收回双向测试的端口

-P, --parallel n
要并行运行的客户端线程数

-T, --ttl n
多播的存活时间（默认为 1）

-Z, --linux-congestion <algo>
设置 TCP 拥塞控制算法（仅限 Linux）
```