---
layout: post
title: "设计模式 创建型：生成器"
date: 2014-04-05 16:29:57 +0800
comments: true
categories: 设计模式
---
### 生成器 Builder
**定义**

Wiki[链接][1]。又名：建造模式，是一种对象构建模式。它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。

<!--more-->
**用户场景**

我们已生成汽车做为例子。虽然不同的车厂生成不同品牌的汽车，但是很多零部件的通用的，不同品牌的汽车只是把他们组合起来。比如轮胎选择米其林或固特异，变速箱采用ZF或爱信。工厂在为某个新品牌新建生产线的时候只要选择不同的供应商就可以了，而不需要所有零件都自己从头生产。例子代码如下。

```cpp
#include <iostream>
#include <string>

using namespace std;

class Tire {};  // 轮胎
class GoodYearTire : public Tire {};  // 固特异轮胎
class MichelinTire : public Tire {};  // 米其林轮胎

class GearBox {}; // 变速箱
class ZFGearBox : public GearBox {};  // ZF变速箱
class AWGearBox : public GearBox {};  // 爱信变速箱

class Car {
  public:
    Car(string name, Tire *tires, GearBox *gearBox) {
      _name = name;
      _tires = tires;
      _gearBox = gearBox;
    }

    Car() {
      delete[] _tires;
      delete _gearBox;
    }

    void showInfo() {
      cout << "I am a " << _name << " car." << endl;
    }

protected:
  string _name;
  Tire *_tires;       // 轮胎
  GearBox *_gearBox;  // 变速箱
};

// 生产线
class CarBuilder {
  public:
    virtual Car* produce() = 0;
};

// 高尔夫生产线
class GolfCarBuilder : public CarBuilder {
  public:
    virtual Car* produce() {
      // 使用固特异轮胎和ZF变速箱
      return new Car(string("Golf"), new GoodYearTire[4], new ZFGearBox);
    }
};

// 宝马生产线
class BMWCarBuilder : public CarBuilder {
  public:
    virtual Car* produce() {
      // 使用米其林轮胎和爱信变速箱
      return new Car(string("BMW"), new MichelinTire[4], new AWGearBox);
    }
};

// 汽车工厂
class Factory {
  public:
    virtual Car* produce() = 0;
    virtual void setBuilder(CarBuilder*) = 0;
};

// 大众工厂，我们有高尔夫和宝马两条生产线
class VWFactory : public Factory {
  public:
    VWFactory(CarBuilder *carBuilder) {
      _carBuilder = carBuilder;
    }

    void setBuilder(CarBuilder *carBuilder) {
      if (_carBuilder != NULL) {
        delete _carBuilder;
      }

      _carBuilder = carBuilder;
    }

    virtual Car* produce() {
      return _carBuilder->produce();
    }

  protected:
    CarBuilder *_carBuilder;
};

int main(void) {
  CarBuilder *golfCarBuilder = new GolfCarBuilder;
  CarBuilder *bmwCarBuilder = new BMWCarBuilder;
  Factory *vwFactory = new VWFactory(golfCarBuilder);

  Car *golfCar = vwFactory->produce();

  vwFactory->setBuilder(bmwCarBuilder);

  Car *bmwCar = vwFactory->produce();

  golfCar->showInfo();
  bmwCar->showInfo();

  delete bmwCar;
  delete golfCar;
  delete vwFactory;
  delete bmwCarBuilder;
  delete golfCarBuilder;
}
```

[1]: http://zh.wikipedia.org/wiki/%E7%94%9F%E6%88%90%E5%99%A8%E6%A8%A1%E5%BC%8F