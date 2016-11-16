---

layout: post  
title: Java多态机制的理解  
date: 2015-10-25 12:30:01  
tags: [java]  
category: 后台开发  
comments: true

---

面向对象编程有三大特性：封装、继承、多态。  

封装隐藏了类的内部实现机制，可以在不影响使用情况下改变类的内部结构，同时也保护了数据，对于外界而言他的内部细节是隐藏的，暴露给外界的只是他的访问方法

继承所描述的是`“is-a”`（是一个，说明该类是某类的一个特殊例子）的关系，如果有两个对象A和B，若可以描述为“A是B”，则可以表示 A 继承 B，其中 B 是被继承者称之为父类或者超类，A 是继承者称之为子类或者派生类。

多态成立的三个条件  
1.继承  
2.子类重写父类方法  
3.父类引用指向子类对象  
用简述的话说就是：继承、重写、向上转型

从某一个角度来讲，封装和继承都相当于是为多态而做准备   
多态应用的一大前提是继承，以及继承里面的重写方法；继承的一大前提是封装，及封装里面涉及到重要知识点方法重载  
<!-- more -->

## 那么到底什么是多态呢？它的实现机制又是什么？

> 多态用一句话概括可以描述为：事物在运行过程中存在不同的状态；他的重要特性：一个接口，多种实现；指在编译时引用变量调用的方法无法确定是执行A类里面的方法还是B类里面的方法，只有在运行期间才能确定要执行的方法，这样不用修改源代码，就可以改变程序运行时所绑定的具体代码，让程序有多个状态可以选择，这就是多态性

举个可爱的、形象的例子，比如有一个函数方法是对描述动物的特征，方法要求传递的参数要求是动物，但我们并不知道到底是何种动物，只有听着声音才能判断这是什么动物，听到“瞄”叫声，发现这是猫、听到“旺”叫声，发现这是狗、听到“咩”叫声，发现这是羊，于是可以描述成这样：

动物a = 猫  
动物b = 狗  
动物c = 羊

这里猫、狗、羊都是动物的子类，我们可以通过动物这个父类就能够引用到不同的子类，这就是多态；只有在运行的时候才会知道应用变量所指向的具体对象

由上面的例子可以看出，猫(Cat)、狗(Dog)、羊(Sheep)是动物(Animal)的子类，把Animal指向Cat对象。因为Cat是继承的Animal对象，所以Cat可以转为Animal；这样做的好处就是因为子类继承自父类，所以他可以提供比父类更强大的功能，我们定义了一个指向子类的父类引用类型，他除了能引用父类的方法外，还可以使用子类的强大功能，我们把这样的描述称为向上转型

向上转型需要注意的是父类类型的引用只能调用父类中定义的属性和方法，对于只存在子类的方法和属性他就不能调用了，即调用的方法和属性需是在超类中存在的方法

``` java
public class Animal {
    
    public void fun1(){
        System.out.println("Animal fun1 ... ");
        fun2();
    }
	
    public void fun2(){
        System.out.println("Animal fun2 ... ");
    }
}

public class Cat() extends Animal{

    /**
     * 子类重载父类的方法
     * 父类中不存在该方法，向上转型后，父类并不能调用该方法
     */
    public void fun1(String s){
        System.out.println("Cat fun1 ... ");
    }
    
    /**
	 * 子类重写父类的方法
	 * 指向子类的父类引用调用fun2时，必定会调用此方法
	 */
    public void fun2(){
        System.out.println("Cat fun2 ... ");
    }
}


运行结果：
Animal fun1 ... 
Cat fun2 ... 


```

上面的结果由于子类重载了父类的fun1()方法，重写了fun2()方法，重载后的fun1(String s)和fun1()不是同一个方法，父类中由于没有过该方法，向上转型后，丢失了fun1(String s)方法，就执行的父类的fun1()，调用fun2()方法的时候，由于子类重写的fun2()，是能调用到子类的fun2()的


多态定义上就是要通过统一、抽象的形式去完成复杂多变的需求，比如

``` java
animal.features();
```

animal是不同的，但是属于一个派生系，features形式上是相同的，但实现不同，具体定位到哪个实现由animal的本质决定，这对于jvm来说已经非常清楚了，动态加载，动态绑定，简单粗暴的一句话就是调用了相同的方法，出现了不同的结果，这就是多态的表现


总结：因为有向上转型，所以指向子类的父类引用，只能调用父类里面存在的方法和属性；子类重写父类的方法，必定能被父类引用调用


多态有两种实现形式：继承和接口  
上面的例子描述的就是继承的实现，那么接口的实现是怎样的呢？  

``` java
//定义接口InterA  
interface InterA {  
    void fun();  
}  
//实现接口InterA的类B  
class B implements InterA {  
    public void fun() {      
        System.out.println("This is B");  
    }  
}  

//实现接口InterA的类C  
class C implements InterA {  
    public void fun()  {      
        System.out.println("This is C");  
    }  
}  

class Test{  
    public static void main(String[] args){  
        InterA a;  
        a= new B();  
        a.fun();   
        a = new C();   
        a.fun();   
    }  
} 

输出结果为：
This is B
This is C
```

上例中，B类和C类是InterA接口的实现类，分别实现了接口fun()，通过将B类和C类的实例赋给接口引用a实现了方法在运行时动态绑定，也满足了“一个接口，多种实现”的特性，展出出了java的多态性

总结：同继承实现相似，java在利用接口调用实现类的方法的时候，该方法也必须是在接口中存在，并在实现类中被重写；

对比继承和接口两种实现形式，继承由于是单继承，但接口可以多实现，所以在进行扩展的时候，接口相比继承的方式有更好的灵活性


通过一个实例巩固下，这个实例包含了太多的知识，是关于多态的一个非常经典的例子：

``` java
class A {
    public String show(D obj) {
        return ("A and D");
    }
    public String show(A obj) {
        return ("A and A");
    } 
}

class B extends A{
    public String show(B obj){
        return ("B and B");
    }
    public String show(A obj){
        return ("B and A");
    } 
}

class C extends B{
	
}

class D extends B{
	
}

class Test {
    public static void main(String[] args) {
        A a1 = new A();
        A a2 = new B();
        B b = new B();
        C c = new C();
        D d = new D();
        
        System.out.println("1--" + a1.show(b));
        System.out.println("2--" + a1.show(c));
        System.out.println("3--" + a1.show(d));
        System.out.println("4--" + a2.show(b));
        System.out.println("5--" + a2.show(c));
        System.out.println("6--" + a2.show(d));
        System.out.println("7--" + b.show(b));
        System.out.println("8--" + b.show(c));
        System.out.println("9--" + b.show(d));      
    }
}
```

上例实例输出的最终结果为：

``` java
1--A and A
2--A and A
3--A and D
4--B and A
5--B and A
6--A and D
7--B and B
8--B and B
9--A and D
```

先看比较好理解的1、2、3，①会到A类去找，但由于没发现B类型，B类型向上转型为A，最终执行的效果就是a1.show(A obj);②同理也是到A类去找，由于没有发现C类型，也是由C向上转型为A，最终执行效果同①类似，打印都是“A and A”，③这里直接A类能找到直接打印

但遇到第④个就相对有点模糊了，很多人认为是“B and B”，但事实不是，这里会涉及到多态的两个知识点  
1、当超类对象引用变量引用子类对象时，被引用对象的类型而不是引用变量的类型决定了调用谁的成员方法，但是这个被调用的方法必须是在超类中定义过的，也就是说被子类覆盖的方法。
2、方法调用的优先问题 ，优先级由高到低依次为：this.show(O)、super.show(O)、this.show((super)O)、super.show((super)O)

对于④来说，a2.show(b)，类型为A，则this为a2，b是B的一个实例，于是到A里面去找show(B obj)方法，没有找到，于是到A的super(超类)找，A由于没有超类，转到this.show((super)O)优先级查找，this还是为a2，O为B，(super)O即(super)B，翻译一下就是(A)B，其实就是转成了A，所以最后是在A里面去找show(A obj)方法，发现有了这个方法，但这儿还没有完，涉及到上面的知识点1，“被引用对象的类型而不是引用变量的类型决定了调用谁的成员方法，但是这个被调用的方法必须是在超类中定义过的”，这里引用变量是A类型，引用对象是B，所以是按照引用对象来确定调用的是谁的方法，就是调用的B类里的show(A obj)，但条件是知识点中的“被调用的方法必须是在超类中定义过的”条件要满足才行，因为这里show(A obj) 也是在超类(A)里面被定义过的，所以最终打印的是“B and A”

依照此原理可以用这种方法推断其他答案