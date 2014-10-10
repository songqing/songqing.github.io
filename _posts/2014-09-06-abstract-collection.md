---
layout: post
title:  AbstractCollection
date:   2014/09/04 20:23:14 
category: life
tag: life
---

> 此类提供 Collection 接口的骨干实现，以最大限度地减少了实现此接口所需的工作。
> 
> 要实现一个不可修改的 collection，编程人员只需扩展此类，并提供 iterator 和 size 方法的实现。（iterator 方法返回的迭代器必须实现 hasNext 和 next。）
> 
> 要实现可修改的 collection，编程人员必须另外重写此类的 add 方法（否则，会抛出 UnsupportedOperationException），iterator 方法返回的迭代器还必须另外实现其 remove 方法。
> 
> 按照 Collection 接口规范中的建议，编程人员通常应提供一个 void （无参数）和 Collection 构造方法。
> 
> 此类中每个非抽象方法的文档详细描述了其实现。如果要实现的 collection 允许更有效的实现，则可以重写这些方法中的每个方法。
> 
> java自带的集合是一个常用数据结构的集合，也是在平常用得比较多的地方，相当于一个比较常用的数据结构，自带框架设计思想，实现思路都非常具有参考思想。


集合框架主要的类和接口如下图所示（来自于百度百科）：



<!--![Alt text]({{ BASE_PATH }}/images/java-collction-framework.jpg "java集合框架图")-->
<img src="{{ BASE_PATH }}/images/java-collction-framework.jpg" width="950" hight="970">

从上面的图可以看出，主要是两个接口，一个Collection接口和一个Map接口，所以不要把Collection和Collections搞混，也不要把集合框架记混，只要记住Map和Collection完全没有联系即可。


###实现的主要方法

###重点：
1：看他的contains方法实现可以看到,他借助了iterator方法，如果一个AbstractCollection为null。使用contains(null)，会返回真,如果子类没有覆盖该方法，即如何contains需要看子类的iterator和覆盖contains方法


