---
title: "containerd安装"
date: 2022-02-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["containerd"]
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


debian 11安装(建议debian内核为5.11以后)

1. 需要安装内容
containerd、runc、CNI plugins、nerdctl(客户端)
1. 安装containerd
```
a. 进入下载对应版本https://github.com/containerd/containerd/releases
wget https://github.com/containerd/containerd/releases/download/v1.7.2/containerd-1.7.2-linux-amd64.tar.gz
b. shell> tar Cxzvf /usr/local containerd-1.6.2-linux-amd64.tar.gz
bin/

c. 配置开机启动服务
 服务配置文件内容：https://raw.githubusercontent.com/containerd/containerd/main/containerd.service

 vim /etc/systemd/system/containerd.service
----------------
# Copyright The containerd Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

[Unit]
Description=containerd container runtime
Documentation=https://containerd.io
After=network.target local-fs.target

[Service]
#uncomment to enable the experimental sbservice (sandboxed) version of containerd/cri integration
#Environment="ENABLE_CRI_SANDBOXES=sandboxed"
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/local/bin/containerd

Type=notify
Delegate=yes
KillMode=process
Restart=always
RestartSec=5
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNPROC=infinity
LimitCORE=infinity
LimitNOFILE=infinity
# Comment TasksMax if your systemd version does not supports it.
# Only systemd 226 and above support this version.
TasksMax=infinity
OOMScoreAdjust=-999

[Install]
WantedBy=multi-user.target
-----------------

shell> systemctl daemon-reload
shell> systemctl enable --now containerd
```
3. 安装runc
```
下载对应安装文件：https://github.com/opencontainers/runc/releases

shell> install -m 755 runc.amd64 /usr/local/sbin/runc
```

4. 安装CNI plugins
```
下载对应安装文件：https://github.com/containernetworking/plugins/releases

shell> mkdir -p /opt/cni/bin
shell> tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.1.1.tgz
```

5. 安装nerdctl
```
下载对应安装文件：https://github.com/containerd/nerdctl/releases
shell> tar Cxzvvf /usr/local/bin nerdctl-1.4.0-linux-amd64.tar.gz

配置：Rootless
a. 安装依赖：
shell> sudo apt-get purge apparmor apparmor-utils auditd
shell> sudo apt-get install uidmap
shell> sudo apt-get install slirp4netns

特殊配置：
1. 开启overlay   
echo "options overlay permit_mounts_in_userns=1" > /etc/modprobe.d/mdadm.conf
2. 开启配置cgroup v2
shell> vim /etc/default/grub
------
GRUB_CMDLINE_LINUX="systemd.unified_cgroup_hierarchy=1"
------
shell> sudo update-grub.

--------
Enabling CPU, CPUSET, and I/O delegation
$ cat /sys/fs/cgroup/user.slice/user-$(id -u).slice/user@$(id -u).service/cgroup.controllers
memory pids

$ sudo mkdir -p /etc/systemd/system/user@.service.d
$ cat <<EOF | sudo tee /etc/systemd/system/user@.service.d/delegate.conf
[Service]
Delegate=cpu cpuset io memory pids
EOF
$ sudo systemctl daemon-reload
---------

shell> containerd-rootless-setuptool.sh install

iptable 问题：
I had the same issue and in my system (debian bullseye) iptables and ip6tables was installed only under /usr/sbin. A quick fix was to make a link in /usr/local/bin

ln -s /usr/sbin/iptables /usr/local/bin/iptables
ln -s /usr/sbin/ip6tables /usr/local/bin/ip6tables
```