---
layout: post
title: "设计模式 创建型：抽象工厂"
date: 2014-04-01 21:46:00 +0800
comments: true
categories: 设计模式
---
### 抽象工厂 Abstract Factory
**定义**

[Wiki链接](http://zh.wikipedia.org/wiki/%E6%8A%BD%E8%B1%A1%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F)。为一个产品族提供了统一的创建接口。当需要这个产品族的某一系列的时候，可以从抽象工厂中选出相应的系列创建一个具体的工厂类。

<!--more-->
**用户场景**

还是继续“[工厂模式]()”关于坦克生产的话题。话说我们准备修改代码，不再搞单独的坦克工厂、军舰厂和其它厂了，取而代之的是统一的兵工厂。也就是说俄国的兵工厂生产苏联的武器，美国的兵工厂生产美国的武器。反面例子就不举了，要问的还是两个老问题：1、新增加一个国家的兵工厂怎么处理？2、如果代码已二进制发布，别人如何方便的进行扩展？


解决方案和工厂方法很像，只不过现在的工厂提供了一组方法。先定义一个兵工厂接口，声明一系列方法告诉别人我要生产那些装备，剩下的就是各个具体兵工厂继承并实现这些方法。如果我们套定义的话，就是为一系列武器的创建提供了统一的接口，在需要的时候我们选择某个具体的兵工厂实现来完成任务。例子代码如下。

``` cpp main.cpp
#include <iostream>

using namespace std;

class Weapon {};

class Tank : public Weapon {};
class USTank : public Tank {};
class RussiaTank : public Tank {};

class Warship : public Weapon {};
class USWarship : public Warship {};
class RussiaWarship : public Warship {};

// 兵工厂
class Armory {
  public:
    virtual Tank* generateTank() = 0;
    virtual Warship* generateWarship() = 0;
};

// 美国兵工厂
class USArmory : public Armory {
  public:
    virtual Tank* generateTank() {
      cout << "US generates a Tank." << endl;
      return new USTank;
    }

    virtual Warship* generateWarship() {
      cout << "US generate a Warship." << endl;
      return new USWarship;
    }
};

// 俄国兵工厂
class RussiaArmory : public Armory {
  public:
    virtual Tank* generateTank() {
      cout << "Russia generates a Tank." << endl;
      return new RussiaTank;
    }

    virtual Warship* generateWarship() {
      cout << "Russia generate a Warship." << endl;
      return new RussiaWarship;
    }
};

int main(void) {
  Armory *pUSArmory = new USArmory;
  Armory *pRussiaArmory = new RussiaArmory;

  Tank *pUSTank = pUSArmory->generateTank();
  Warship *pUSWarship = pUSArmory->generateWarship();

  Tank *pRussiaTank = pRussiaArmory->generateTank();
  Warship *pRussiaWarship = pRussiaArmory->generateWarship();

  delete pRussiaWarship;
  delete pRussiaTank;
  delete pUSWarship;
  delete pUSTank;
  delete pRussiaArmory;
  delete pUSArmory;
}
```
