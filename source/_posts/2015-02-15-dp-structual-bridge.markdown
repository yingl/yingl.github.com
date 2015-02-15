---
layout: post
title: "设计模式 结构型：桥接"
date: 2015-02-15 12:44:07 +0800
comments: true
categories: 设计模式
---
### 桥接 Bridge
**定义**

Wiki[链接][1]。号称是最复杂的设计模式之一。它把事物对象和其具体行为、具体特征分离开来，使它们可以各自独立的变化。

<!--more-->
设想一下我们要开发一个绘图程序，需要支持不同的形状和不同的颜色，这时我们有以下两种方案可选：

- 为某种形状和颜色的组合提供一个实现版本。
- 根据设计方案对形状和颜色进行组合。

如果选择第一个方案，一旦排列组合爆炸的话我们就处理不过来了。而第二个方案则可以像搭积木一样自由选择，情况就简单多了。

在例子代码中，我们通过形状的抽象基类CShape把颜色CColor组合进来，这样我们就可以非常轻松的实例化出不同形状和颜色的组合。

```cpp
#include <iostream>

using namespace std;

class CColor {
  public:
    virtual void paint() const = 0;
};

class CRed : public CColor {
  public:
    void paint() const { cout << "Paint by RED." << endl; }
};

class CBlue : public CColor {
  public:
    void paint() const { cout << "Paint by BLUE." << endl; }
};

class CShape {
  public:
    CShape(const CColor *pColor) { _pColor = pColor; }

    virtual void draw() = 0;
  protected:
    const CColor *_pColor;
};

class CCircle : public CShape  {
  public:
    CCircle(const CColor *pColor) : CShape(pColor) {}

    void draw() {
      cout << "Circle: ";
      _pColor->paint();
  }
};

int main(void) {
  CColor *pColor = new CRed();
  CShape *pShape = new CCircle(pColor);
  pShape->draw();
}
``` 

[1]: http://zh.wikipedia.org/wiki/%E6%A9%8B%E6%8E%A5%E6%A8%A1%E5%BC%8F