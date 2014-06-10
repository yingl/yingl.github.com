---
layout: post
title: "设计模式 创建型：单例"
date: 2014-06-10 21:10:19 +0800
comments: true
categories: 设计模式
---
### 单例 ingleton
**定义**

[Wiki链接](http://zh.wikipedia.org/wiki/%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F)。单例对象的类必须保证只有一个实例存在。

为什么需要单例，其实理由很简单，为避免重复建设。比如我们前面讲到的工厂模式，相同的工厂只要有一个就够了，多了就浪费了，管理起来也不方便。下面具体讲一下单例的实现。

实现一（一般面试的标准答案）
{% codeblock lang:cpp %}
lock g_lock;

class Singeleton {
  public:
    static Obj* getInstance() {
      if (NULL == _instance()) {
        // 加锁确保线程安全
        lock(&g_lock);
        // 这部判断很重要，很有可能其它线程刚调用完getInstance，_instance已被创建。
        // 如果这里不判断是否NULL，很可能又创建一个新的Obj对象。
        if (NULL == _instance()) {
          _instance = new Obj;
        }
        unlock(&g_lock);
      }
      
      return _instance;
    }

  protected:
    static Obj *_instance;
};

Obj* Singeleton::_instance = NULL;
{% endcodeblock %}

实现二（利用静态变量简化多线程的情况）
{% codeblock lang:cpp %}
template<class T> class Singleton {
  public:
    static T* getInstance() {
      static T _instance;

      return &_instance;
    }   
};
{% endcodeblock %}