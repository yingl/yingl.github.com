---
layout: post
title: "设计模式 结构型：外观"
date: 2014-07-03 21:22:17 +0800
comments: true
categories: 设计模式
---
### 外观 Facade
**定义**

[Wiki链接](http://zh.wikipedia.org/zh-cn/%E5%A4%96%E8%A7%80%E6%A8%A1%E5%BC%8F)。它为子系统中的一组接口提供一个统一的高层接口，使得子系统更容易使用。。

<!--more-->
这个模式估计是所有设计模式里最简单的了。我们以计算机为例，一个开机动作涉及到CPU启动，硬盘读取，内存装载等一系列动作，但是对于用户来说，只要按一下电源键就可以了。我们可以把开机抽象成计算机对象的一个方法，为了完成一次开机，它完成了所有涉及CPU、内存和硬盘的相关操作。

{% codeblock lang:cpp %}
#include <iostream>

using namespace std;

class CPU {
public:
  void freeze(void) {
    cout << "CPU freeze" << endl;
  }

  void jump(void) {
    cout << "CPU jump" << endl;
  }

  void execute(void) {
    cout << "CPU execute" << endl;
  }
};

class Memory {
public:
  void load(void) {
    cout << "Memory load" << endl;
  }
};

class HardDisk {
public:
  void read(void) {
    cout << "HardDisk read" << endl;
  }
};

class Computer {
private:
  CPU cpu;
  Memory memory;
  HardDisk hard_disk;

public:
  void power_on(void) {
    cpu.freeze();
    hard_disk.read();
    memory.load();
    cout << "Computer power on" << endl;
  }
};

int main(void) {
  Computer computer;

  computer.power_on();
}
{% endcodeblock %}