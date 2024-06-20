---
weight: 5
title: "virtualenv虚拟环境使用"
date: 2024-06-20T11:04:49+08:00 
lastmod: 2024-06-20T11:04:49+08:00
draft: false
author: "langhaidian"
authorLink: "https://langhaidian.io"
description: "virtualenv虚拟环境使用."
resources:
- name: "featured-image"

tags: ["virtualenv python"]
categories: ["python"]

lightgallery: true
---


  *python的虚拟环境有很多选择，今天只记录virtualenv简单使用*

##### 1. 安装virtualenv
```
pip install virtualenv 
```
##### 2. 创建虚拟环境并指定python版本,会在本目录下创建下虚拟环境
```
virtualenv venv --python=pythonx.x.x 
```
##### 3. 进入虚拟环境
```
source venv/bin/activate 
```
##### 4. 退出虚拟环境
```
deactivate 
```
##### 5. 删除虚拟环境
```
rm -rf venv
```