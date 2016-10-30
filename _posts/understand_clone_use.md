---

layout: post   
title: 理解clone()方法如何使用  
date: 2016-09-12 16:42:01  
tags: [java]  
category: 后台开发  
comments: true  

---

在实际编程过程中，我们常常要遇到这种情况：有一个对象A，在某一时刻A中已经包含了一些有效值，此时可能会需要一个和A完全相同新对象B，并且此后对B任何改动都不会影响到A中的值，也就是说，A与B是两个独立的对象，但B的初始值是由A对象确定的

于是，在这种情况下，clone就可以派的上用场了
<!--more-->

Clone有缺省行为，super.clone();他负责产生正确大小的空间，并逐位复制，使用clone()来复制一个对象，clone()从Object类继承。所有具有clone功能的类都有一个特性，那就是它直接或间接地实现了Cloneable接口。

我们可以来看一看JDK的Object源码里clone接口

```
protected native Object clone() throws CloneNotSupportedException;
```

可以看出它是一个protected方法,所以我们不能简单地调用它；关键字native，表明这个方法使用java以外的语言实现

Clone的使用方式：

```
public class User implements Cloneable{
	private String name;
	private int age;
	private int sex;
	
	public User(){}
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
	public int getSex() {
		return sex;
	}
	public void setSex(int sex) {
		this.sex = sex;
	}
	@Override
	protected Object clone() throws CloneNotSupportedException {
		return (User)super.clone();
	}
	@Override
	public String toString() {
		return "User [name=" + name + ", age=" + age + ", sex=" + sex + "]";
	}
}
```

示例：

```
User u = new User();
System.out.println(u.clone()!=u);
System.out.println(u.clone().getClass()==u.getClass());
System.out.println(u.clone().toString().equals(u.toString()));
/*返回的值都为true*/
```

说明：  
1、拷贝对象返回的是一个新对象，而不是一个引用  
2、拷贝对象与用new操作符返回的对象区别在于这个拷贝包含了一些原来对象的信息，而不是对象的初始化信息

浅拷贝(浅克隆)和深拷贝(深克隆)的概念:  
浅拷贝：被复制对象的所有变量都含有与原来的对象相同的值，而所有的对其他对象的引用仍然指向原来的对象。换言之，浅复制仅仅复制所考虑的对象，而不复制它所引用的对象。  

深拷贝：被复制对象的所有变量都含有与原来的对象相同的值，除去那些引用其他对象的变量。那些引用其他对象的变量将指向被复制过的新对象，而不再是原有的那些被引用的对象。换言之，深复制把要复制的对象所引用的对象都复制了一遍。
