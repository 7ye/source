---

layout: post   
title:  Java三大特性之封装
date: 2014-10-24 16:42:01  
tags: [java]  
category: 后台开发  
comments: true  

---

封装是面向对象程序设计四大基本概念之一，其余三个分别是继承，多态和抽象。

封装是一种可以使类中的字段私有并能通过公有方法来访问私有字段的技术。如果一个字段被声明为私有（private），它就不能在类的外部被访问，从而隐藏了类内部的字段。基于这个原因，封装有时也被称为“数据隐藏”。

封装可以被认为是一种能够保护代码和数据被定义在类外的其它代码任意访问的屏障。访问数据和代码由一个接口严格控制。 封装的主要好处是修改我们实现的代码而又不会破坏其他人使用我们的代码。封装的这个特性使我们的代码具有可维护性、灵活性以及扩展性。 --摘自：维基百科

## 封装的优点
- 1、良好的封装能够减少耦合。
- 2、类内部的结构可以自由修改。
- 3、可以对成员变量进行更精确的控制。
- 4、隐藏信息，实现细节。
<!-- more -->

## 实现步骤
1、修改属性的可见性来限制属性的访问，这就要得益于java中private、protected、public各种修饰符的访问权限了，例如：

``` java
public class Man {
  private String name;
  private int age;
}
```

这段代码中，把name和age用private修饰符设置为私有的，只有本类才能访问，其他的类都访问不了，这样相当于实现了上述摘要里面所说的“数据隐藏”
2、对属性值提供对外的公共访问方法，创建一对赋值、取值方法，作用于访问私有属性，例如：
```
public class Man {
	
  private String name;
  private int age;
    
  public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```

采用 this 关键字是为了解决实例变量（private String name）和局部变量（setName(String name)中的name变量）之间发生的同名的冲突。

## 实例
创建一个Man类

``` java
public class Man {
	
	//属性封装，男人有姓名、年龄、妻子皆为这个对象的私有属性
	private String name;
	private int age;
	private Woman wife;
	
	/*
	 * 方法封装
	 * 对该对象提供外界访问方法的封装，可以设定姓名、年龄、妻子，也可以获取姓名和年龄
	 */
	
	public String getName() {
    return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public void setWife(Woman wife) {
		this.wife = wife;
	}
}
```

创建一个Woman类

``` java
public class Woman {

	//属性封装
	private String name;
	private int age;
	private Man husband;
	
	//方法封装
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public Man getHusband() {
		return husband;
	}
	public void setHusband(Man husband) {
		this.husband = husband;
	}
}
```

在Man类中，可以看见没有提供getWife()方法，这是因为男人不想让自己妻子被外界访问，这样起了保护作用不是  

所以封装就是可以把一个对象的属性私有，而提供一些可以被外界访问的方法，以上实例中public方法是外部类访问该类成员变量的入口。

以Man类的name属性为例，他有get和set方法，get即为获取值，set即为赋值，用的public修饰符修饰，可以被外界所访问，在比如说Man的wife属性，就没有ge方法，男人都想金屋藏娇，外界是不能获取到wife属性的

问题继续，上面的例子还无法详细表现出封装的特性，封装的主要好处是修改我们实现的代码而又不会破坏其他人使用我们的代码，用一个例子：

``` java
public void setAge(int age) {
  if(age>200){
      System.out.println("年龄输入过大！");
  }else{
      this.age = age;
  }
}

```

上例中就是一个对封装特性的一个很好的表现，在输入年龄的时候不小心输入200岁的人妖就可以进入到判断里面给人提示出来了，调用者还是使用的Woman.getAge()来调用获取age值的，没有修改任何代码，但我们在在getAge()方法里面可以有一些扩展，不用担心会造成多处更改的情况，相对维护性来说，非常方便
