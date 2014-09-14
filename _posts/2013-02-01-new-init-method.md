---
layout: post
category : javaBasic
date: 2013/01/31 01:00:00 
title: 之前没有用到过的一种初始化对象的方法
tags : javaBasic
---


##概述
之前我们初始化对象的时候，如果是一个接口，我们通常是实例化实现类，如果是类，我们直接通过new来实例化一个对象，但是今天看到了另外一种方法，记下来，也许很少用到，但是争取在心中留个印象

##接口声明和实现如下


{% highlight java %}

package cn.liuyiyou.javase.basic;

/**
 * User: liuyiyou
 * Date: 13-2-1
 * Time: 下午8:01
 */
public interface MyInterface {

    void sayHello();
}

package cn.liuyiyou.javase.basic;

/**
 * User: liuyiyou
 * Date: 13-2-1
 * Time: 下午8:02
 */
public class MyInterfaceImpl implements MyInterface {

    @Override
    public void sayHello() {
        System.out.println("Say Hello");
    }
}



{% endhighlight %}




###类
{% highlight java %}


package cn.liuyiyou.javase.basic;

/**
 * User: liuyiyou
 * Date: 13-2-1
 * Time: 下午8:10
 */
public class MyClass {

    public void sayHello(){
        System.out.println("SayHello ");
    }
}

{% endhighlight %}




##常规初始化方式

###接口初始化


{% highlight java %}


 @Test
    public void testSayHello() throws Exception {

        MyInterface myInterface = new MyInterfaceImpl();

        myInterface.sayHello();
    }


{% endhighlight %}



###类初始化

{% highlight java %}

  public void testSayHello() throws Exception {

       MyClass myClass = new MyClass();
    }


{% endhighlight %}



##新的初始化方法

###接口初始化

{% highlight java %}

	@Test
    public void testSayHello2() throws Exception {

        MyInterface myInterface = new MyInterface() {
            @Override
            public void sayHello() {
                System.out.println("Say Hello In Test");
            }
        }  ;

        myInterface.sayHello();
    }

{% endhighlight %}



###类初始化

{% highlight java %}

	@Test
    public void testSayHello2() throws Exception {

        new MyClass(){
            @Override
            public void sayHello() {
                super.sayHello();    //To change body of overridden methods use File | Settings | File Templates.
            }
        };
    }

{% endhighlight %}

##总结使用场景

也就是说，初始化类或者接口的时候，可以用新的初始化方法覆盖默认实现接口。而不需要为了使用这个类而写一个类来继承这个类或者实现这个接口，只需要在初始化的时候重写这个方法即可。

比如在线程中，不管是继承Thread或者实现Runnable接口都需要重写run方法类，在这种情况下，如果run方法比较简单，则可以使用这种新方法
比如：

{% highlight java %}


public void type1(){
        Thread thread = new Thread("type1"){
           public void run(){
               System.out.println("start");
           }
        } ;
        thread.start();

    }

{% endhighlight %}



