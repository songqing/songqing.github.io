---
layout: post
category : Design pattern
date: 2014/05/02 00:56:52 
title: 设计模式之单例模式
tags : Design_pattern
---
{% include JB/setup %}

###概述

在它的核心结构中只包含了一个被称为单例类的特殊类。通过单例模式可以保持系统中一个类只有一个实例而该实例易于被外界访问，从而他方

###特征

* 持有一个该类的静态成员变量
* 私有的构造方法
* 一个对外公开的静态工厂方法

###使用方式：

第一种：饿汉式：在类初始化时，已经自行实例化

<pre class="brush: java;">

    public class Singleton1 {

    //在自己内部定义自己的一个实例，只供内部调用
    private static Singleton ourInstance = new Singleton();

    public static Singleton getInstance() {
        return ourInstance;
    }


    private Singleton1() {
    }
}
</pre>

第二种：懒汉式：在需要实例化的时候才进行实例化，有线程同步和序列化问题

所谓线程安全是指：如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。
如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。或者说：
一个类或者程序所提供的接口对于线程来说是原子操作或者多个线程之间的切换不会导致该接口的执行结果存在二义性
,也就是说我们不用考虑同步的问题。显然以上实现并不满足线程安全的要求，在并发环境下很可能出现多个Singleton实例。

<pre class="brush: java;">

    public class Singleton {

    private Singleton singleton;

    private Singleton(){

    }

    private Singleton getInstance(){
        if (singleton == null)
            singleton2 = new Singleton2();
        return singleton;
    }
}
</pre>

第三种：使用枚举类，可以解决多线程问题，这是efftive java中的一个实现方式

<pre class="brush: java;">
    
    public enum Singleton6 {
    
    INSTANCE;
}
</pre>

第四种：登记式单例类.将类名注册，下次从里面直接获取


<pre class="brush: java;">
    public class Singleton3 {
    private static Map<String, Singleton3> map = new HashMap<String, Singleton3>();
    static {
        Singleton3 single = new Singleton3();
        map.put(single.getClass().getName(), single);
    }

    // 保护的默认构造子
    protected Singleton3() {
    }

    // 静态工厂方法,返还此类惟一的实例
    public static Singleton3 getInstance(String name) {
        if (name == null) {
            name = Singleton3.class.getName();
            System.out.println("name == null" + "--->name=" + name);
        }
        if (map.get(name) == null) {
            try {
                map.put(name, (Singleton3) Class.forName(name).newInstance());
            } catch (InstantiationException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
        }
        return map.get(name);
    }

    // 一个示意性的商业方法
    public String about() {
        return "Hello, I am RegSingleton.";
    }

    public static void main(String[] args) {
        Singleton3 single3 = Singleton3.getInstance(null);
        System.out.println(single3.about());
    }
}
</pre>

###优点

1. 单例模式会组织其他对象实例化单例对象的副本，从而确保所有对象都访问唯一实例
2. 因为类控制了实例化过程，这个类可以灵活更改实例化过程

###缺点

1. 开销。虽然数量很少，但是如果每次其他对象使用时候都要检查是否存在该类的实例（这个是针对懒汉式）
2. 线程不安全的问题。（针对懒汉式）

