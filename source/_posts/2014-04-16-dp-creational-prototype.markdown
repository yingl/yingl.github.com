---
layout: post
title: "设计模式 创建型：原型"
date: 2014-04-16 01:53:44 +0800
comments: true
categories: 设计模式
---
### 原型 Prototype
**定义**

[Wiki链接](http://zh.wikipedia.org/wiki/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F)。通过“复制”一个已经存在的实例来返回新的实例,而不是新建实例。被复制的实例就是我们所称的“原型”，这个原型是可定制的。原型模式多用于创建复杂的或者耗时的实例，因为这种情况下，复制一个已经存在的实例使程序运行更高效；或者创建值相等，只是命名不一样的同类数据。

<!--more-->
**用户场景**

Wiki已经讲的很清楚了。简单来说就是把一个复杂的对象创建过程简化成复制粘贴的形式。需要注意的是我们在复制原型的时候使用深拷贝还是浅拷贝。此外该模式还经常和工厂模式一起使用，因为原型可以用工厂来构建。看了以下例子后可能会有一个疑问，为什么要提供一个clone方法，而不是直接调用拷贝构造函数？因为C++是静态语言，我们在复制原型时并不知道它是具体那个类型，而clone的话这个类自己负责拷贝，只需要返回一个基类类型的指针就可以了。

``` cpp main.cpp
#include <iostream>
#include <string>

using namespace std;

class Prototype {
  public:
    virtual Prototype* clone() const = 0;
};

class Product : public Prototype {
  public:
    Product() {
      cout << "Take long time to create a product." << endl;
    }   

    Product(const Product& product) {
      cout << "Copy a product is so fast." << endl;
    }   

    Prototype* clone() const {
      return new Product(*this);
    }   
};

int main(void) {
  Prototype *p1 = new Product;
  Prototype *p2 = p1->clone();
  Prototype *p3 = p2->clone();
}
```