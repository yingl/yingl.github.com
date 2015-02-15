---
layout: post
title: "设计模式 结构型：修饰"
date: 2015-02-15 15:20:01 +0800
comments: true
categories: 设计模式
---
### 修饰 Decorator
**定义**

Wiki[链接][1]。一种动态地往一个类中添加新的行为的设计模式。就功能而言，修饰模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能。

比如我们设计一个UI库，需要能灵活的给一个文本框添加垂直滚动、水平滚动功能。如果用继承的方式很容易因为排列组合导致失控，而依靠修饰模式我们可以自由的对各种扩展功能进行有选择的排列组合。

```cpp
#include <iostream>

using namespace std;

class Widget {
  public:
    virtual void draw() const = 0;
};

class TextBox : public Widget {
  public: 
    void draw() const { cout << "TextBox" << endl; }
};

class Decorator : public Widget {
  public:
    Decorator(const Widget *pWidget) { _pWidget = pWidget; }

    void draw() const { _pWidget->draw(); }

  protected:
    const Widget *_pWidget;
};

class HScrollDecorator : public Decorator {
  public:
    HScrollDecorator(Widget *pWidget) : Decorator(pWidget) {}

    void draw() const {
      Decorator::draw();
      cout << "HScroll is supoorted!" << endl;
    }
};

class VScrollDecorator : public Decorator {
  public:
    VScrollDecorator(const Widget *pWidget) : Decorator(pWidget) {}

    void draw() const {
      Decorator::draw();
      cout << "VScroll is supoorted!" << endl;
    }
};

int main(void) {
  Widget *pWidget = new HScrollDecorator(
                      new VScrollDecorator(
                        new TextBox));
  pWidget->draw();
}
```

[1]: http://zh.wikipedia.org/wiki/%E4%BF%AE%E9%A5%B0%E6%A8%A1%E5%BC%8F