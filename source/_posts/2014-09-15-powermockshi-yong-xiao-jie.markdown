---
layout: post
title: "PowerMock使用小结"
date: 2014-09-15 16:48:05 +0800
comments: true
categories: 测试分享
---
最近在写单元测试的时候大量使用了PowerMock，遇到不少问题，通过google逐一解决，在这里和大家分享一下。关于为什么使用PowerMock，最主要的优势在于它支持mock静态和私有成员函数。

持续更新中...
<!--more-->
首先我们实现一个被测类，包含公有、私有和静态方法。

关于为什么要mock在这里做一下简单的解释，大家不嫌啰嗦的话可以看一看。单元测试的要点在于快和简单。快在于执行速度快，简单在用例简单和发现问题后定位简单。所以为达到这个目标，我们在测试的时候可以把所有不相关的部分都做一个信任假设（这里信任指按期望工作，可以期望正确，也可以期望错误），通过mock，我们可以轻松控制这些偶尔对象的行为，快速的构造测试场景。比如我们在测试displayFullUsername方法时可以假设其引用的私有和静态方法总是按期望工作的，对于私有方法toUpperCase我们再另外写单元测试来保证正确性，这样当displayFullUsername单元测试失败时，我们不用去debug私有方法toUpperCase相关的代码，只用关心当前的代码逻辑就可以了。另外，一些函数依赖的方法背后可能有个非常复杂的系统，测试环境的配置就直接把你搞晕，这种时候mock就可以把问题大大简化了。

{% codeblock lang:java %}
public class Demo {
    private String username;
    private int userId;

    public void setUsername(String username) {
        this.username = username;
    }

    public String getUsername() {
        return username;
    }

    public void setUserId(int userId) {
        this.userId = userId;
    }

    public int getUserId() {
        return userId;
    }

    public static String generateRandomPrefix() {
        return "Random";
    }

    private String toUpperCase(String str) {
        return str.toUpperCase();
    }

    public int hackUserId(int userId) {
        return userId * 100 / 7;
    }

    public String displayFullUsername() {
        String fullUsername = generateRandomPrefix() + "_" + toUpperCase(username) + "_" + hackUserId(userId);
        return fullUsername;
    }

    public String handleMiniType(MiniType miniType) {
        return "randomString";
    }

    public static void main(String[] args) {
        Demo demo = new Demo();
        demo.setUsername("monster");
        demo.setUserId(1234);
        System.out.println(demo.displayFullUsername());
    }
}
{% endcodeblock %}

记得先在POM文件里添加以下依赖：
{% codeblock lang:xml %}
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.8.1</version>
    <scope>test</scope>
</dependency>
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
{% endcodeblock %}

首先我们在测试类前面添加2个注解，告诉JUnit我们要使用PowerMock的Runner，以及需要mock的类。
{% codeblock lang:java %}
@RunWith(PowerMockRunner.class)
@PrepareForTest({Demo.class})
public class DemoTest {
    // ...
}
{% endcodeblock %}

在setUp函数里初始化我们要mock的测试对象，测试类里声明了一个private Demo demo对象
{% codeblock lang:java %}
@Before
public void setUp() throws Exception {
    demo = PowerMockito.spy(new Demo());
    PowerMockito.mockStatic(Demo.class);
}
{% endcodeblock %}
注意，如果不用spy的话对于未mock的正常函数就无法执行。另外我们需要mock静态方法generateRandomPrefix所以调用了mockStatic。

现在我们第一个测试用例展示了如何mock不同类型的方法。
{% codeblock lang:java %}
@Test
public void testDisplayFullUsername_001()  throws Exception {
    // mock私有方法
    PowerMockito.doReturn("ABCD").when(demo, "toUpperCase", "abcd");
    // mock静态方法
    PowerMockito.when(Demo.generateRandomPrefix()).thenReturn("Random_001");
    // mock公有方法，anyInt表示接受任何int类型参数。
    PowerMockito.when(demo.hackUserId(anyInt())).thenReturn(3000);
    demo.setUserId(1234);
    demo.setUsername("monster");
    String str = demo.displayFullUsername();
    Assert.assertEquals("Random_001_MONSTER_3000", str);
}
{% endcodeblock %}

对私有方法的测试也很简单。
{% codeblock lang:java %}
@Test
public void testToUpperCase_001() throws Exception {
    String str = Whitebox.<String>invokeMethod(demo, "toUpperCase", "xyz");
    Assert.assertEquals("XYZ", str);
}
{% endcodeblock %}

如果需要用到输入参数，可以考虑使用Answer，例子代码如下：
{% codeblock lang:java %}
@Test
public void testHackUserId_001() throws Exception {
    PowerMockito.when(demo.hackUserId(anyInt())).thenAnswer(new Answer<Object>() {
        @Override
        public Object answer(InvocationOnMock invocation) throws Throwable {
            Object[] args = invocation.getArguments();
            return Integer.parseInt(args[0].toString()) * 3;
        }
    });

    Assert.assertEquals(3005, demo.hackUserId(1000));
}
{% endcodeblock %}

另外，在使用PowerMock的时候注意以下几个地方：

1. 对私有函数的mock尽量写成PowerMockito.doReturn(返回值).when(对象名, 方法名, 参数列表)的形式，不要写成PowerMockito.when(...).thenReturn(...)，不然会有可能返回各种奇奇怪怪的错误。
2. 如果函数参数是自定义的类，比如要mock方法handleMiniType，我们就需要使用ArgumentCaptor，如果我们mock的时候是希望对于某个任意值。代码如下：
{% codeblock lang:java %}
    @Test
    public void testHandleMiniType() throws Exception {
        ArgumentCaptor<MiniType> arg = ArgumentCaptor.forClass(MiniType.class);
        PowerMockito.when(demo.handleMiniType(arg.capture())).thenReturn("Idonotknow")
        Assert.assertEquals("randomString", demo.handleMiniType(new MiniType()));
    }
{% endcodeblock %}