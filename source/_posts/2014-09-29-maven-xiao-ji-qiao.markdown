---
layout: post
title: "maven-小技巧"
date: 2014-09-29 09:34:53 +0800
comments: true
categories: 小技巧
---

最近在使用maven过程中发现一个问题，在更新了pom.xml文件后在idea中重新导入，但是相关的包还是没有找到。检查了一下发现jar包没有下载到本地。好吧，检查服务器，发现jar包是存在的，也能下载到本地。那问题就好办了，在本地用命令行方式安装就可以了。

mvn install:install-file -DgroupId=cn.monstersay.blog -DartifactId=blog-web -Dversion=1.0.0 -Dpackaging=jar -Dfile=blog-web-1.0.0-20140915.053502-20.jar