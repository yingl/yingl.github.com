---
layout: post
title: "Clazz.this和this的区别"
date: 2015-02-28 17:09:27 +0800
comments: true
categories: Java
---

最近在看某项目源代码，经常看到类似这样的代码"doSomething(XXXFragment.this, ...)"，这个和直接调用this有什么区别？为了搞清楚这个概念，在参考了网上的资料后写了这么一段例子代码：

```Java
public class MyObject {
  public void method() {
    new Runnable() {
      public void run() {
        System.out.println(this.toString());
        System.out.println(MyObject.this.toString());
      }

      public String toString() {
        return "Anonymous objects's Runnable!";
      }
    }.run();
  }

  public String toString() {
    return "MyObjects's object!";
```

执行结果如下：
Anonymous objects's Runnable!
MyObjects's object!

可以看出，当一个inner类必须使用outer类的this实例时，就必须使用Clazz.this，使得编译器知道使用正确的this实例。