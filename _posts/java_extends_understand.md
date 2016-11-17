---

layout: post  
title: Java三大特性之继承   
date: 2014-10-24 01:30:01  
tags: [java]  
category: 后台开发  
comments: true

---

Java继承是面向对象的最显著的一个特征。继承是从已有的类中派生出新的类，新的类能吸收已有类的数据属性和行为，并能扩展新的能力

在多个类中存在相同属性和行为时，将这些内容单独抽取到一个类中，那么多个类就不需再次定义这些类和属性，只要继承那个类就行了

在继承的关系里，被继承的类称为父类、超类或者基类，而继承的类就被称为子类。子类继承了父类的所有属性（包括private成员，并不可访问）和方法，通过继承可以对对象描述更加清晰，<font color="red">提高代码的复用性</font>。不仅如此，子类还可以根据自己的独特性，扩展属性和方法。

## 如何实现
通过 `extends` 关键字让类与类之间产生继承关系。语法：`public calss 子类名 extends 父类名{}`

``` java
public calss Cat extends Animal{}
```
<!-- more -->

例:
创建一个Animal(动物)类

``` java
public class Animal {

    public void fun1(){
        System.out.println("Animal fun1 ... ");
    }
    
    public void fun2(){
        System.out.println("Animal fun2 ... ");
    }
}
```

创建一个Cat(猫)类，然后让Cat类继承自Animal

```java
public class Cat extends Animal {

}
```

这样就实现了继承，Cat就是Animal的子类  

就这样实现了一个继承貌似也没有什么特别的意义，继承的意义在于让子类和父类可以产生差异，我们来详细的看看怎样让子类和父类产生差异的，主要有两种方法

### 第一种方法，直接在子类加方法
直接在子类中添加新的方法和成员变量。这意味着父类的成员变量或方法不能满足子类的需要，因此需要添加新的来满足子类的需求，以上面的Cat为例

``` java
public class Cat extends Animal {
	
    //新增方法
    public void skill(){
        System.out.println("猫会抓鱼");
    }
    
    public static void main(String[] args) {
        Cat c = new Cat();
        c.fun1();
        c.skill();
    }
}

打印结果：
Animal fun1 ... 
猫会抓鱼

```

上例方法中猫的技能在Animal类中没有方法能满足，所以添加了一个新的方法满足了需求，既能调用父类的方法，有能调用自己特有的扩展方法

### 第二种方法，覆盖(overriding)父类的方法，还可以被叫做重写
overriding的作用不是要添加父类没有的方法，而是覆盖父类的方法，（前提是父类方法访问权限不是private，且子类方法权限大于等于父类方法），这里会涉及到几个访问权限修饰符的知识点，不懂可以特别研究下

``` java
public class Cat extends Animal {
	
    /**
     * 子类重写父类的方法
     */
    @Override
    public void fun2() {
        System.out.println("Cat fun2 ... ");
    }
    
    public static void main(String[] args) {
        Cat c = new Cat();
        c.fun1();
        c.fun2();
    }
}

打印结果：
Animal fun1 ... 
Cat fun2 ... 
```

这里对上述代码有两个点需要进行一下描述：  
1、大家也看见了fun2()方法上有一个`@Override`，@Override表示下面出现的方法会出现重写操作，由编译器检查，如果达不到重写的条件，就会报错。一定程度上增加了代码的安全性和健壮性，可有可无，为了可读性高，留下更好  
2、覆盖的方法必须与父类定义完全相同，注意这里是方法名和返回值及参数都要相等，拿参数来说，如果参数变了，那么这就不能叫覆盖（重写）了，而是“重载”，这里不深入重载的研究了，不过重写的方法修饰符是可以改变的，不过还是得遵循（子类方法权限大于等于父类方法）定律   

好，回到上面的例子，这里打印的是`Animal fun1 ...`和`Cat fun2 ... `，说明fun1()由于继承的关系执行的是父类的方法，如果Cat里面没有fun2()方法的话肯定也会执行父类里面的fun2()方法，但这里由于子类（Cat）覆盖了父类的fun2()方法，所以执行到了Cat.fun2();最终打印`Cat fun2 ...`，这就表明overriding成功了

## 继承后的子类运行顺序
子类构造器中的this的调用 → 父类成员变量初始化 → 父类构造方法 → 子类变量初始化 → 子类构造方法  
究其原因，是子类因为继承特性，所以拥有了父类的数据，也就是成员变量，而子类在写这些数据时，必须先知道父类的如何定义或者初始化的  

```java
public class Animal {
	
    public Animal(){
        System.out.println("这是动物类");
    }
}

public class Cat extends Animal {

    public Cat(){
        System.out.println("这是猫类");
    }
    
    public static void main(String[] args) {
        Cat c = new Cat();
    }
}

打印结果：
这是动物类
这是猫类

```

可见就算是没有构造方法，创建了Cat对象后，也是先调用的父类方法，后调用子类方法   

总结：  
1、子类重写父类的方法，必须同父类方法相同
2、子类重写父类的方法，子类方法权限需大于等于父类方法
3、子类继承父类后调用的运行顺序依次是：父类成员变量初始化 → 父类构造方法 → 子类变量初始化 → 子类构造方法
