---
layout: post
title: "Bugs: 价格转换粗大事了"
date: 2014-05-20 15:12:48 +0800
comments: true
categories: Bugs
---

今天中午刚吃完饭正在一狼假寐，突然电话铃响起！什么？居然有价格为0的商品？立刻从睡梦中惊醒开始排查。啥都不说了，先把肇事代码拿出来：

<!--more-->
{% codeblock lang:javascript %}
function getPriceStr(priceLong) {
  if (isNaN(priceLong)) {
    return '';
  }
  return (Math.round(priceLong / 100)).toFixed(2);
}
{% endcodeblock %}

很明显，问题出在round上，丫返回的是整数啊！后台返回的价格单位是分，前台转化成元，想法没错，但是但是...好吧，测试也有很大责任，只看页面展现，把这个细节漏掉了。从这个例子可以看出，单元测试非常非常重要，如果有单元测试的话，开发阶段就可以把这个问题修复了。还好这次没撞上大活动，在灰度阶段被兄弟部门发现。