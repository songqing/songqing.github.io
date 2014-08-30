---
layout: post
category : Design pattern
date: 2014/05/01 00:56:52 
title: 设计模式值单例模式
tags : Design pattern
---
{% include JB/setup %}

<h3>概述</h3>
在它的核心结构中只包含了一个被称为单例类的特殊类。通过单例模式可以保持系统中一个类只有一个实例而该实例易于被外界访问，从而他方
<h3>特征：</h3>
<ul>
    <li>持有一个该类的静态成员变量</li>
    <li>私有的构造方法</li>
    <li>一个对外公开的静态工厂方法</li>
</ul>
<h3>使用方式：</h3>
<p>第一种：饿汉式：在类初始化时，已经自行实例化
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

<p>第二种：懒汉式：在需要实例化的时候才进行实例化，有线程同步和序列化问题</p>
<p>但是以上实现没有考虑线程安全问题。所谓线程安全是指：如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。
  如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。或者说：
  一个类或者程序所提供的接口对于线程来说是原子操作或者多个线程之间的切换不会导致该接口的执行结果存在二义性
  ,也就是说我们不用考虑同步的问题。显然以上实现并不满足线程安全的要求，在并发环境下很可能出现多个Singleton实例。
 </p>
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

<p>第三种：使用枚举类，可以解决多线程问题，这是efftive java中的一个实现方式</p>
<pre class="brush: java;">
    
    public enum Singleton6 {
    
    INSTANCE;
}
</pre>

<p>第四种：登记式单例类.将类名注册，下次从里面直接获取</p>

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

            <h3>优点</h3>

            <h4>
            一、实例控制</h4>
            单例模式会阻止其他对象实例化其自己的单例对象的副本，从而确保所有对象都访问唯一实例。

            <h4>二、灵活性</h4>
            因为类控制了实例化过程，所以类可以灵活更改实例化过程。

            <h3>缺点</h3>

            <h4>一、开销</h4>
            虽然数量很少，但如果每次对象请求引用时都要检查是否存在类的实例，将仍然需要一些开销。可以通过使用静态初始化解决此问题。
            二、可能的开发混淆
            使用单例对象（尤其在类库中定义的对象）时，开发人员必须记住自己不能使用new关键字实例化对象。因为可能无法访问库源代码，因此应用程序开发人员可能会意外发现自己无法直接实例化此类。
            三、对象生存期
            不能解决删除单个对象的问题。在提供内存管理的语言中（例如基于.NET Framework的语言），只有单例类能够导致实例被取消分配，因为它包含对该实例的私有引用。在某些语言中（如 C++），其他类可以删除对象实例，但这样会导致单例类中出现悬浮引用。

