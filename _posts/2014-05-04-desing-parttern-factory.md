---
layout: post
category : Design pattern
date: 2014/05/02 00:56:52 
title: 设计模式之简单工厂模式
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
