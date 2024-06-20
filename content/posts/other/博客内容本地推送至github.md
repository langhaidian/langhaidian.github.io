---
weight: 5
title: "博客内容本地推送至github"
date: 2022-02-10T11:04:49+08:00 
lastmod: 2022-02-10T11:04:49+08:00  
draft: false
author: "langhaidian"
authorLink: "https://langhaidian.io"
description: "博客内容本地推送至github"
resources:
- name: "featured-image"
  src: "/img/docker-logo.png"

tags: ["hugo"]
categories: ["hugo"]

lightgallery: true
---

#### 1. github创建空的仓库，给予xxxxx.io名字，公开权限

#### 2. 克隆仓库至本地
  ```
  git clone https://github.com/xxxxxxx
  ```

#### 3. 本地hugo环境以及博客创建完成，本地关联远程github仓库
  ```
  git remote add origin git@github.com:miawithcode/xxxxx
  ```

#### 4.  本地编辑完的博客内容实用如下命令进行提交至github
  ```
  git pull --rebase origin main   # 首次与远端同步
  git add .
  git commit -m "...(修改的信息)"
  git push origin main
  ```
  