---
title: "博客内容本地推送至github"
date: 2022-02-10T11:04:49+08:00 
# weight: 1
# aliases: ["/first"]
tags: ["virtualenv python"]
categories: ["blog"]
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

##### 1. github创建空的仓库，给予xxxxx.io名字，公开权限

##### 2. 克隆仓库至本地
  ```
  git clone https://github.com/xxxxxxx
  ```

##### 3. 本地hugo环境以及博客创建完成，本地关联远程github仓库
  ```
  git remote add origin git@github.com:miawithcode/xxxxx
  ```

##### 4.  本地编辑完的博客内容实用如下命令进行提交至github
  ```
  git pull --rebase origin main   # 首次与远端同步
  git add .
  git commit -m "...(修改的信息)"
  git push origin main
  ```
  