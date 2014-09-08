---
layout: post
title:  java集合框架概要
date:   2014/09/04 20:23:14 
category: life
tag: life
---

java自带的集合是一个常用数据结构的集合，也是在平常用得比较多的地方，相当于一个比较常用的数据结构，自带框架设计思想，实现思路都非常具有参考思想。

集合框架主要的类和接口如下图所示（来自于百度百科）：



![Alt text]({{ BASE_PATH }}/images/java-collction-framework.jpg "java集合框架图")

从上面的图可以看出，主要是两个接口，一个Collection接口和一个Map接口，所以不要把Collection和Collections搞混，也不要把集合框架记混，只要记住Map和Collection完全没有联系即可。

###先看几个常用的接口吧：
Collection接口：根借口（别把Map放进去），主要是Set，queue，List

List：有序可重复，可以根据索引快速访问元素

Set：无序不可重复

Queue：队里，先入先出

Map：键值对

其他的话主要看后缀：

以set结尾的都实现了Set接口，比如EnumSet，SortedSet，HashSet，TreeSet，LinkedHashSet

以ue结尾的都是实现了Queue接口，比如Deque，PiorityQueue

以List结尾的都实现了List接口，比如ArrayList，LinkedList

其中LinkedList，实现了Dequeue和List接口，Vector实现了List接口

Stack继承了Vector接口

以Map结尾的都实现了Map接口，比如EnumMap，IdentifyHashMap，HashMap,LinkeHashMap，WeakHashMap，SortedMap，TreeMap。

其中Properties继承了HashTable。

接下来，逐个击破。

首先是Collection：

Collection是集合继承树的跟接口，一个集合表示一组对象（即元素），有的集合允许重复元素，有的不允许重复元素。有的是已经有序的，有的是无序的。

JDK并没有提供直接实现这个接口的类。而是提供了子接口，比如Set，List，用来进行进一步分类。这是什么设计模式？

> Collection 层次结构 中的根接口。Collection 表示一组对象，这些对象也称为 collection 的元素。一些 collection 允许有重复的元素，而另一些则不允许。一些 collection 是有序的，而另一些则是无序的。JDK 不提供此接口的任何直接 实现：它提供更具体的子接口（如 Set 和 List）实现。此接口通常用来传递 collection，并在需要最大普遍性的地方操作这些 collection。
> 
> 包 (bag) 或多集合 (multiset)（可能包含重复元素的无序 collection）应该直接实现此接口。
>
> 所有通用的 Collection 实现类（通常通过它的一个子接口间接实现 Collection）应该提供两个“标准”构造方法：一个是 void（无参数）构造方法，用于创建空 collection；另一个是带有 Collection 类型单参数的构造方法，用于创建一个具有与其参数相同元素新的 collection。实际上，后者允许用户复制任何 collection，以生成所需实现类型的一个等效 collection。尽管无法强制执行此约定（因为接口不能包含构造方法），但是 Java 平台库中所有通用的 Collection 实现都遵从它。
>
> 此接口中包含的“破坏性”方法，是指可修改其所操作的 collection 的那些方法，如果此 collection 不支持该操作，则指定这些方法抛出 UnsupportedOperationException。如果是这样，那么在调用对该 collection 无效时，这些方法可能，但并不一定抛出 UnsupportedOperationException。例如，如果要添加的 collection 为空且不可修改，则对该 collection 调用 addAll(Collection) 方法时，可能但并不一定抛出异常。
>
> 一些 collection 实现对它们可能包含的元素有所限制。例如，某些实现禁止 null 元素，而某些实现则对元素的类型有限制。试图添加不合格的元素将抛出一个未经检查的异常，通常是 NullPointerException 或 ClassCastException。试图查询是否存在不合格的元素可能抛出一个异常，或者只是简单地返回 false；某些实现将表现出前一种行为，而某些实现则表现后一种。较为常见的是，试图对某个不合格的元素执行操作且该操作的完成不会导致将不合格的元素插入 collection 中，将可能抛出一个异常，也可能操作成功，这取决于实现本身。这样的异常在此接口的规范中标记为“可选”。
>
> 由每个 collection 来确定其自身的同步策略。在没有实现的强烈保证的情况下，调用由另一进程正在更改的 collection 的方法可能会出现不确定行为；这包括直接调用，将 collection 传递给可能执行调用的方法，以及使用现有迭代器检查 collection。
>
> Collections Framework 接口中的很多方法是根据 equals 方法定义的。例如，contains(Object o) 方法的规范声明：“当且仅当此 collection 包含至少一个满足 (o==null ? e==null :o.equals(e)) 的元素 e 时，返回 true。”不 应将此规范理解为它暗指调用具有非空参数 o 的 Collection.contains 方法会导致为任意的 e 元素调用 o.equals(e) 方法。可随意对各种实现执行优化，只要避免调用 equals 即可，例如，通过首先比较两个元素的哈希码。（Object.hashCode() 规范保证哈希码不相等的两个对象不会相等）。较为常见的是，各种 Collections Framework 接口的实现可随意利用底层 Object 方法的指定行为，而不管实现程序认为它是否合适。
>
> 此接口是 Java Collections Framework 的一个成员。

主要方法：

{% highlight java %}

/**
* 返回Collection中的元素个数，如果此 collection 包含的元素大于 Integer.MAX_VALUE，
*则返回 Integer.MAX_VALUE，也就是说集合中的元素是有限制的。并不是无限的
*/
int size();

boolean isEmpty();

/**
*如果此 collection 包含指定的元素，则返回 true。更确切地讲，当且仅当此 collection
* 至少包含一个满足 (o==null ? e==null : o.equals(e)) 的元素 e 时，返回 true。
*/
boolean contains(Object o);

Iterator<E> iterator();

Object[] toArray();

<T> T[] toArray(T[] a);

boolean add(E e);	

boolean remove(Object o);

boolean containsAll(Collection<?> c);

boolean addAll(Collection<? extends E> c);

boolean removeAll(Collection<?> c);

boolean retainAll(Collection<?> c);

void clear();

boolean equals(Object o);

int hashCode();


{% endhighlight %}

今天才知道，原来集合在不考虑内存的情况下也不是无穷大的！


