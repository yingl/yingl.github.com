---
layout: post
title: "PowerMock简介"
date: 2014-09-15 16:48:05 +0800
comments: true
categories: 测试分享
---
工作多年以来发现很多团队对自动化测试的理解就是写脚本，单元测试就是用XUnit框架写脚本。团队话费了大量精力去写脚本但取得的实际价值确不容乐观。比如在测试中我想测试数据库读写异常，网络请求返回特定的错误等等...

<!--more-->
以上这些想法如果通过正常手段去模拟会遇到很多困难，而且会导致测试对外部组件、环境有很强的依赖，还会导致代码难以维护。为了方便起见，我在单元测试的编写过程中选用了PowerMock框架来实现mock功能。

之所以选用PowerMock是因为它弥补了iMock, EasyMock和Mockito框架等的一个共同缺点：不支持静态，私有和final方法。具体使用的时候非常方便，因为PowerMock是对现有框架进行扩展，基本遵循相同的语法。我在github上放了一个[例子][1]，演示了几个常用的场景。

Maven配置：在pom.xml中添加以下引用，这里使用了基于Mockito的实现。
```xml
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-api-mockito</artifactId>
    <version>1.5.5</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-module-junit4</artifactId>
    <version>1.5.5</version>
    <scope>test</scope>
</dependency>
```

测试代码前添加如下注解，把你需要mock的类放在PrepareForTest里，不然运行时会报错。
```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({DemoSpy.class})
public class DemoSpyTest {
  // ...
}
```

[1]: https://github.com/yingl/PowerMockDemo