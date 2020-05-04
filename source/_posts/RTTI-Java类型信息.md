---
title: RTTI--Java类型信息
date: 2020-05-03 14:50:07
tags: Java
---







### 为什么需要RTTI

有时候，当我们得到一个基类引用却不知道该引用具体指向的对象类型，而我们又需要根据引用指向的不同对象类型做不同的事情。此时，我们需要做的是在运行时先判断引用指向的具体类型。这就是RTTI的意义所在：运行时，识别一个对象的类型。

### Class对象

每个类都会有且只有一个Class对象，它通过JVM的类加载器加载到内存中。Class对象包括一个类的所有信息，包括类名、域、方法等。有了它，我们就可以识别一个对象的具体类型或者得到某个类的所有信息。

获取Class对象的方式

1.getClass()方法。例如，我们有一个java.lang包下的对象引用o，可以通过：

```java
Class c = o.getClass();
```

<!-- more -->

2.当我们知道某个类的全限定名，不需要等到该类的对象也可以获取该类的Class对象：

```java
Class c = Class.forName("java.lang.String");
```

3.类字面常量：

```java
Class c = String.class;
```

区别，使用1和2的方式获取Class对象对立即执行类的初始化，方式3没有执行类的初始化。

虚拟构造器，通过Class的newInstance()方法，我们可以创建该类的对象，但该类必须有默认构造器。

泛化的Class引用。Class类可以使用泛型语法，有了泛型语法，可以强制编译器执行额外的类型检查，减少了在运行时所需要的类型检查和错误。

```java
Class<String> c = String.class;
c = Integer.class; //编译错误
```



### 类型转换前先做检查

在多态的使用场景中，我们常常会得到一个基类的引用却不知道它指向的对象的具体类型。在我们把它进行向下转型的时候就有可能抛出ClassCastException。instanceof关键字能帮助我们判断一个引用指向的对象是否属于某个类



### 反射：运行时的类信息

Java提供了一系列类来表示一个类的所有信息，包括类的域（Field）、方法（Method）、构造器（Constructor）、父类、注解（Annotation）等。有了这些类，我们可以在运行时通过对象的形式来表示一个类的所有信息，这就是反射。

### 动态代理

有了反射技术，我们可以在运行时做一些额外的事情而又不影响到我们原来代码。例如，在方法中加入堆栈信息和性能信息的打印，一些我们想把它们跟主程序逻辑分离开的东西。

代理时基本的设计模式之一，它是你为了提供额外的或不同的操作，而插入的用来代理“实际”对象的对象。这些操作通常设计与“实际”对象的通信，所以代理通常充当一个中间人的角色。就像下面的例子：

```java
interface Interface{
	void doSomething();S
}

class RealObject implements Interface{
	public void doSomething(){
		System.out.print("do something");
	}
}

class SimpleProxy implements Interface{
	private Interface proxied;
	public SimpleProxy(Interface proxied){
		this.proxied = proxied;
	}
	public void doSomething(){
		//do something else
		System.out.print("do something else");
		proxied.doSomething();
	}
}

class DynamicProxyHandler implements InvocationHandler {
    private Object proxied;
    public DynamicProxyHandler(Object proxy) {
        this.proxied = proxy;
    }
    
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return method.invoke(proxied, args);
    }
}

public class ProxyDemo{
    static void customer(Interface iface){
        iface.doSomething();
    }
    
    public static void main(String[] args){
        //简单代理
        customer(new SimpleProxy(new RealObject()));
        
        //动态代理
        RealObject realObject = new RealObject();
        Interface proxy = (Interface)Proxy.newProxyInstance(
                realObject.getClass().getClassLoader(),
                new Class[]{Interface.class},
                new DynamicProxyHandler(realObject));
        proxy.doSomething();
    }
}
```



### 空对象

### 接口与类型信息

interface关键字可以让程序员对不同的构件进行隔离，降低程序的耦合性。但RTTI会将这种耦合性传播开来，因此interface不是一种无懈可击的解耦方案。



###### 