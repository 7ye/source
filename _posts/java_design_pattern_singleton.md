---

layout: post   
title: Java设计模式之 - 单例模式  
date: 2015-11-12 17:43:01  
tags: [java, 设计模式]  
category: 后台开发  
comments: true  

---

单例模式（Singleton Pattern）是Java中最简单的设计模式之一，这种类型的设计模式属于“创建者”模式  
## 介绍
概述：单例模式的意思就是只有一个实例。单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。这个类称为单例类  
作用：Singleton模式主要作用是保证在Java应用程序中，一个类Class只有一个实例存在  
注意点：构造函数是私有的，避免外界利用构造方法直接创建出实例   
使用场景：
* 1、要求生成唯一的序列号
* 2、web中的计数器，不用常去数据库更新，可以先用单例缓存起来
* 3、创建对象比较耗资源的操作，比如 I/O 与数据库的连接
* 4、web中的共享的资源配置对象读取也可以用单例
等等......

<!--more-->

优点：
* 1、节省内存开销，限制了实例的个数，不用频繁去创建

缺点： 
* 1、单例模式没有抽象层，不易扩展
* 2、单例类的职责过重，在一定程度上违背了“单一职责原则”。因为单例类既充当了工厂角色，提供了工厂方法，同时又充当了产品角色，包含一些业务方法，将产品的创建和产品的本身的功能融合到一起。
* 3、滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出；现在很多面向对象语言（如Java、C#）的运行环境都提供了自动垃圾回收的技术，因此，如果实例化的对象长时间不被利用，系统会认为它是垃圾，会自动销毁并回收资源，下次利用时又将重新实例化，这将导致对象状态的丢失。
    
## 实现
Singleton的实现有多种方式，一般单例都是五种写法。饿汉，懒汉，双重校验锁，枚举和静态内部类。

1、饿汉

``` java
public class EagerSingleton {

    private static final EagerSingleton singleton = new EagerSingleton();
    
    private EagerSingleton(){}
    
    public static EagerSingleton getInstance(){
        return singleton;
    }
}
```

2、懒汉

``` java
public class LazySingleton {

    private static LazySingleton singleton = null;
    
    private LazySingleton(){}
    
    public static synchronized LazySingleton getInstance(){
        if(singleton == null){
            singleton = new LazySingleton();
        }
        return singleton;
    }
}
```

3、双重检查锁定（算是懒汉式的一种改进）

``` java
public class Singleton {

    private Singleton(){}
    
    private volatile static Singleton singleton = null;
    
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized (Singleton.class) {
                if(singleton == null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

4、枚举

``` java
public enum EnumSingleton {
    INSTANCE;
    public void whateverMethod(){
        //...
    }
}
```

5、静态内部类

``` java
public class StaticInsideClassSingleton {

    private static class SingletonHolder{
        private static final StaticInsideClassSingleton instance = new StaticInsideClassSingleton();
    }
	
    private StaticInsideClassSingleton(){}
	
    public static final StaticInsideClassSingleton getInstance(){
        return SingletonHolder.instance;
    }
}
```

  
整理途中参考的一些文章  
[http://www.cnblogs.com/ykt/archive/2011/11/24/2261251.html](http://www.cnblogs.com/ykt/archive/2011/11/24/2261251.html)  
[http://www.oschina.net/code/snippet_107039_6062](http://www.oschina.net/code/snippet_107039_6062)  
[http://www.tuicool.com/articles/NVza2am](http://www.tuicool.com/articles/NVza2am)  
[http://www.blogjava.net/kenzhh/archive/2016/03/28/357824.html](http://www.blogjava.net/kenzhh/archive/2016/03/28/357824.html)  
