---
layout: post
title: "Bugs: 优化要小心"
date: 2015-01-22 10:33:35 +0800
comments: true
categories: Bugs
---

最近某个移动客户端发布新版本后有新用户反映安装后经常出现随机接口请求错误，经过日志排查是某些请求过于频繁导致后台暂时屏蔽。但是为什么会导致如此频繁的请求呢？记得开发同学们信誓旦旦的拍着胸脯说这块代码逻辑没改过啊～～～还是老规矩代码说话，把当前版本的git提交日志拉出来，好吧，相关代码有个优化...呃，真的只是优化吗？让我们先来看一下优化后的伪代码。

<!--more-->
```java
void init() {
  dataAdapter = new DataAdapter();
}

void onMessageReceived(Result result) {
  if ((result != null) && (result.getCount() == 0)) {
    receiveMessage();
  }
  // ...
}
```
仔细分析一下代码问题就来了，对于老的已经有消息的用户，这段代码没有问题，但是对于新安装用户，服务端一条消息没有，那么问题就来了。当传入的result的count为0，调用receiveMessage向服务端请求，返回的result的count还是为0，结果就陷入了死循环，你丫好歹加个重试次数限制呢。再来看一下所谓优化前的代码。

```java
void onMessageReceived(Result result) {
  if (dataAdapter == null) {
    dataAdapter = new DataAdapter();
    if ((result != null) && (result.getCount() == 0)) {
      receiveMessage();
    }
  }
  // ...
}
```

在原来的代码里，仅当dataAdapter没有初始化的时候，也就是系统第一次初始化的时候，会尝试重新向服务器请求一次信息，查看receiveMessage代码可以发现，第一次请求是读取本地缓存，可能会因为各种原因返回的count是0。初始化完成之后如果再取到count为0的情况，有可能是网络不好，用户手动刷新一下就好了，不影响正常使用。

事后沟通因为开发认为dataAdaper的初始化应该放到init中会比较清晰，结果没有正确解读原有代码的逻辑。所以没事不要随便优化，做优化之前一定要先写好单元测试，不然改完之后，哼哼！