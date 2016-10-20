---

layout: post  
title: Integer与int的种种比较  
date: 2016-09-16 02:28:01  
tags: [java]  
category: 后台开发  
comments: true  

---

如果别人问Integer和int的区别是什么？  
可能很多人很自然的就想到了以下两点：  
1、Integer是int的包装类  
2、int的初始值为0，Integer的初始值为null

但是，如果别人在问一下
Integer i1 = 1;  
int i2 = 1;  
i1==i2 为true还是false？  

Integer i3 = 1;  
Integer i4 = 1;  
i3==i4 为true还是false？

要求解释一下，为什么会是这个结果，可能会有一些人脑袋就有点懵，虽说这很基础，但很多人没有这么深入研究过，这里对他进行一下总结；
<!--more-->

首先我们先来看一段代码

```
public class TestInteger {
    public static void main(String[] args) {
        int i1 = 128;
        Integer i2 = 128;
        Integer i3 = new Integer(128);

        //Integer 和 int 进行比较会自动拆箱成int
        System.out.println(i1 == i2); //true
        System.out.println(i1 == i3); //true

        Integer i4 = 127; //Java在编译的时候，被翻译成Integer i4 = Integer.valueOf(127);
        Integer i5 = 127;
        System.out.println(i4 == i5); //true

        Integer i6 = 128;
        Integer i7 = 128;
        System.out.println(i6 == i7); //false

        Integer i8 = new Integer(128);
        Integer i9 = new Integer(128);
        System.out.println(i8 == i9); //false
    }
}
```

第一处，i1和i2比较，i1和i3比较，结果都为true，在JDK1.5以上，Integer和int比较都会进行自动拆箱，所以这里为true  
第二处，i4和i5比较的时候为true，但i6和i7进行比较的时候为false，在这里就会有非常多的人不知道是为什么，这里其实跟编译有关

```
Integer i4 = 127;  //在Java编译的时候，被翻译成Integer i4 = Integer.valueOf(127);
```

所以，这里我们在JDK源码里面查看一下valueOf(int i);函数源码的实现

```
/**
 * Returns a <tt>Integer</tt> instance representing the specified
 * <tt>int</tt> value.
 * If a new <tt>Integer</tt> instance is not required, this method
 * should generally be used in preference to the constructor
 * {@link #Integer(int)}, as this method is likely to yield
 * significantly better space and time performance by caching
 * frequently requested values.
 *
 * @param  i an <code>int</code> value.
 * @return a <tt>Integer</tt> instance representing <tt>i</tt>.
 * @since  1.5
 */
public static Integer valueOf(int i) {
    final int offset = 128;
    if (i >= -128 && i <= 127) { // must cache 
        return IntegerCache.cache[i + offset];
    }
    return new Integer(i);
}
```

在JDK的源码中，找到一段valueOf(int i);的实现代码，发现JDK在这个实现里对-128到127之间的数进行了缓存(这样对效率和空间上都比较好)，如果数值大于了127，返回的就是一个对象，而如果两边对比的都是大于127的，那么就相当于对比的两个对象，所以会为false

i8和i9不一样，为因为对象不一样，所有为false

总结：  
1、Integer和int进行比较，不管有没有进行new，都会为true，因为会把Integer自动拆箱成int再去比  
2、两个都是非new出来的Integer，如果数值在-128到127之前，则为true，否则为false
3、两个new出来的Integer进行对比，因为对象不一样会为false
