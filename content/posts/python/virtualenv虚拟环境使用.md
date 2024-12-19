---
title: "virtualenv虚拟环境使用"
date: 2022-02-10T11:04:49+08:00
# weight: 1
# aliases: ["/first"]
tags: ["virtualenv python"]
categories: ["python"]
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