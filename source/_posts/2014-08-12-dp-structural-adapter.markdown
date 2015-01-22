---
layout: post
title: "设计模式 结构型：适配器"
date: 2014-08-12 20:39:40 +0800
comments: true
categories: 设计模式
---
### 适配器 Adapter
**定义**

Wiki[链接][1]。将一个类的接口转接成用户所期待的。一个适配使得因接口不兼容而不能在一起工作的类工作在一起，做法是将类别自己的接口包裹在一个已存在的类中。

<!--more-->
这个简单来说就像Mac接显示器需要一个额外的转接头一样，这个转接头扮演的就是适配器的角色。

```cpp
#include <iostream>

using namespace std;

class Monitor {
public:
  void monitor_display() {
    cout << "Monitor display." << endl;  
  }
};

class Adapter {
public:
  void display() {
    _monitor.monitor_display();
  }

protected:
  Monitor _monitor;
};

class Mac {
public:
  void display() {
    _adapter.display();
  }

protected:
  Adapter _adapter;
};

int main(void) {
  Mac mac;

  mac.display();
}
```

[1]: http://zh.wikipedia.org/wiki/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F