---
layout: post
title: "Bugs: 控制逻辑要小心"
date: 2014-09-24 09:40:02 +0800
comments: true
categories: Bugs
---

最近在给某项目写单元测试，话说这单元测试应该开发来写，但是项目紧张人手不足也就没这么多讲究了。遇到一个函数，看着逻辑很简单，不可能出错，但是一测就发现了问题。问题函数代码简化后如下：

{% codeblock lang:java %}
public Info getInfoById(Long id) {
  Info result = null;
  if ((id != null) && (id > 0)) {
    // 是否被初始化
    if (!INIT) {
      // 初始化程序是否正在运行
      if (!RUNNING) {
        init();
      }
      else {
        // 直接查询数据库
        try {
          result = infoDAO.getById(id);
        } catch (Exception e) {
          Logger.error("...");
        }
      }
      return infoDOMap.get(id);
    }
  }
  return result;
}
{% endcodeblock %}

原理看着很简单，如果已经初始化，就直接从缓存map里查找并返回数据，否则检查是否有其它线程在运行init程序，没有的话先init初始化缓存然后从map里查找并返回数据并返回数据。可是单元测试一跑，问题立马暴露，当我直接查询数据库获得结果或者异常抛出后没有直接返回，而是去返回缓存里的结果。这会导致一个问题，当某个线程在初始化的时候，另一个线程去访问了一个没有完全初始化的缓存。要问这个bug怎么发现的？在单元测试里使用mock很容易模拟初始化的各种状态遍历各种控制路径。开发自己测试的时候只走了一条标准路径自然没有发现问题。修改后的代码如下：

{% codeblock lang:java %}
public Info getInfoById(Long id) {
  if ((id != null) && (id > 0)) {
    // 是否被初始化
    if (!INIT) {
      // 初始化程序是否正在运行
      if (!RUNNING) {
        init();
      }
      else {
        // 直接查询数据库
        try {
          return infoDAO.getById(id);
        } catch (DAOException e) {
          Logger.error("...");
          return null;
        }
      }
    }
  }
  return infoDOMap.get(id);
}
{% endcodeblock %}