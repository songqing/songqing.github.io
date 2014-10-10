---
layout: post
category : 多线程
date: 2013/02/02 01:00:00 
title: 多线程总结
tags : 多线程
---


##概述
虽然在项目中很少使用多线程，但是作为一个有理想的程序员，多线程是必须掌握的一部分，所以，复习一下多线程很有必要

##目标
1. 了解什么是线程，以及线程的启动方式
2. 如何中断一个线程
3. 线程的几种状态和线程属性
4. 如何进行同步
5. 什么是阻塞队列
6. 线程安全的集合（和非线程集合）
7. Callable和Future
8. 执行器和同步器

##线程的启动方式步骤：

1. 继承<code>Thread</code>或者实现<code>Runnable</code>接口

2. 重写<code>run()</code>方法，里面用来进行多线程任务

3. 调用<code>Thread#start()</code>或者Runnable接口的实现类的<code>MyClass#start()</code>方法

###几种创建线程的方法




{% highlight java %}



package cn.liuyiyou.javase.thread;

import javafx.concurrent.Task;

import java.util.ArrayList;
import java.util.List;
import java.util.TimerTask;

/**
 * User: liuyiyou
 * Date: 13-02-02
 * Time: 下午7:50
 * Desc:启动线程的几种方式
 */
public class ThreadStart {

    /**
     * 第一种：Thread
     * */
    public void type1(){
        Thread thread = new Thread("type1"){
           public void run(){
               System.out.println("run type1");
           }
        } ;
        thread.start();

    }



    public static void main(String[] args) {
        new ThreadStart().type1();

        new MyThread().start();

        new Thread(new MyRunnable()).start();


        //传入task任务
        new Thread(new TimerTask() {
            @Override
            public void run() {
                System.out.println("run type4");
            }
        }).start();

    }

  }

class MyThread extends Thread{

    @Override
    public void run() {

        System.out.println("run type2");
    }
}

class  MyRunnable implements Runnable{
    @Override
    public void run() {
        System.out.println("run type3");
    }
}


{% endhighlight %}


##中断线程

待写

##线程状态
线程有以下4个状态：

- New：当调用<code>new Thread()</code>创建一个线程的时候，线程还没有开始运行，此时线程处于新生状态，此时还没有运行线程中的代码，在线程可以运行之前，可以做一些准备。

- Runnable：可运行状态（并不是正在运行） 一旦调用<code>Thread.start()</code>方法，该线程就是可运行的了。此时该线程可能正在运行，可能也没有。一个可运行线程在任何时刻也无法确定该线程是否正在运行，而java线程中也没有running状态

- Blocked：被阻塞线程：发生以下情况时，线程进入被阻塞状态：
    - 线程通过<code>Thread.sleep()方法进入睡眠状态
    - 线程调用在一个I/O上被阻塞
    - 线程试图得到一个锁，而该锁被其他线程持有
    - 线程等待某个触发事件


    唤醒的方式：

    - 睡眠时间已过
    - IO完成
    - 得到的锁被释放
    - 某个条件一触发

- Dead：死线程。有两个原因导致线程死亡
    - 因为<code>run</code>方法正常退出自然死亡
    - 因为一个未被捕获的异常而终止了润方法而非正常死亡
    - 可以使用<code>Thread.isAlive()</code>判断线程是否死亡


##线程的优先级

可以通过<code>Thread#setPriority(Thread.XX_PRIORITY)</code>来设置线程优先级，最低为1，最高为10

##守护线程
守护线程惟一的作用就是为其他线程提供服务，当只剩下守护线程时，虚拟机就退出了。
使用方法：<code>Thread#setDaemon(boolean)</code>


##线程组

一组线程，一般是自己归类，当一组线程被设置成线程组之后，可以方便进行集中管理，即可以同时对一组线程进行操作

###线程组代码

这个是错误的，线程组不能中断，原因是，线程启动之后走不到后面



{% highlight java %}


package cn.liuyiyou.javase.thread;

/**
 * User: liuyiyou
 * Date: 13-02-02
 * Time: 下午8:57
 */
public class ThreadGroupTest {

    public void threadGroupTest(ThreadGroup threadGroup){
        Thread thread1 = new Thread(threadGroup,"thread1"){
            @Override
            public void run() {
                while (true){
                    System.out.println("thread1 Run");
                }
            }
        };
        thread1.start();

        Thread thread2 = new Thread(threadGroup,"thread2"){
            @Override
            public void run() {
                while (true){
                    System.out.println("thread2 Run");
                }
            }
        };
        thread2.start();

        Thread thread3 = new Thread(threadGroup,"thread3"){
            @Override
            public void run() {
                while (true){
                    System.out.println("thread3 Run");
                }
            }
        };
        thread3.start();

        Thread thread4 = new Thread(threadGroup,"thread4"){
            @Override
            public void run() {
                while (true){
                    System.out.println("thread4 Run");
                }
            }
        };
        thread4.start();
    }

    public static void main(String[] args) {

        ThreadGroup threadGroup = new ThreadGroup("myGroup");

        new ThreadGroupTest().threadGroupTest(threadGroup);

        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();  //To change body of catch statement use File | Settings | File Templates.
        }

        threadGroup.interrupt();
    }
}



{% endhighlight %}



