---
layout: post
title: "设计模式 创建型：工厂方法"
date: 2014-03-31 22:00:59 +0800
comments: true
categories: 设计模式
---
上周日给振华港机做了一个关于设计模式的培训，同学们反响还不错。我把相关材料整理了一下，将以连载的形式发布在这里与大家一起分享。从我个人的角度并不推荐四人帮的那本经典著作，太理论，太学术。对于大多数人来说，结合实际场景简单快速上手才是王道。淘宝黎叔就说过：技术是要落地的，不然就成了学术。即使是一个好东西，学习成本太高的话，对企业和员工来说性价比就不高了。我们做培训也是，半天就结合实例把3类23种设计模式讲明白。如果看书的话，我推荐《[大话设计模式](http://book.douban.com/subject/2334288/)》。

### 工厂方法 Factory Method
**定义**

[Wiki链接](http://zh.wikipedia.org/wiki/%E5%B7%A5%E5%8E%82%E6%96%B9%E6%B3%95%E6%A8%A1%E5%BC%8F)。定义一个接口用于创建对象，但是让子类决定初始化哪个类。工厂方法把一个类的初始化下放到子类。

<!--more-->
**用户场景**

以游戏红色警报为例，俄国美国都会生产坦克，如果我们需要一个生产坦克的函数负责所有坦克的生产，那么我们该怎么实现？很多同学第一反应是这样的：

{% codeblock lang:cpp %}
Tank* generateTank(int tankType) {
  if (RUSSIA == tankType) {
    return new RussiaTank;
  }
  else if (US == tankType) {
    return new USTank;
  }
  else {
    // ...
  }
}
{% endcodeblock %}

看着也能正确运行。但是问题来了，如果我们想生产法国坦克怎么办？如果我们把代码以二进制库的形式打包在市场上卖给其它公司，别人想生产其它坦克怎么办？如果是我们自己的话还好办，改generateTank函数就可以了，可是买了我们引擎的其它公司怎么办？


先回忆一下面向对象的一条重要原则；开放－封闭。对修改封闭，对扩展开放。如果我们想生产新的坦克，应该采用扩展而不是修改的方式。工厂方法模式开始上场了，理论不多说，直接例子分析来解决现在的问题。首先我们需要一个工厂接口TankFactory，提供一个generate方法。然后继承这个接口，分别定义USTankFactory和RussiaTankFactory，各自实现generate方法生产不同的坦克。generateTank方法的参数由tankType改为TankFactory*，那么我们上层的代码在掌握了各种坦克工厂的信息后，只要把对应坦克工厂作为参数传下来就可以了。在工程实践里面一个最基本的要求就是下层代码改得越少越好。现在附上新的代码以供参考。

{% codeblock lang:cpp %}
#include <iostream>

using namespace std;

class Tank {};
class USTank : public Tank {};
class RussiaTank : public Tank {};

class TankFactory {
  public:
    virtual Tank* generate() = 0;
};

class USTankFactory : public TankFactory {
  public:
    virtual Tank* generate() { 
      cout << "US generates a Tank." << endl;
      return new USTank; }
};

class RussiaTankFactory : public TankFactory {
  public:
    virtual Tank* generate() { 
      cout << "Russia generates a Tank." << endl;
      return new RussiaTank;
    }
};

Tank* generateTank(TankFactory *pTankFactory) {
  return pTankFactory->generate();
}

int main(void) {
  TankFactory *pUSTankFactory = new USTankFactory;
  TankFactory *pRussiaTankFactory = new RussiaTankFactory;

  Tank *pUSTank = generateTank(pUSTankFactory);
  Tank *pRussiaTank = generateTank(pRussiaTankFactory);
    
  delete pRussiaTank;
  delete pUSTank;
  delete pRussiaTankFactory;
  delete pUSTankFactory;
}
{% endcodeblock %}
