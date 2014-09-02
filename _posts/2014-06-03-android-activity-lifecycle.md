---
layout: post
category : Android
date: 2014/06/03 01:00:00 
title: Activity生命周期
tags : Android
---


###概述
Activity生命周期就是一个Activity从开始到结束的全部过程，期间还有有暂停和重新开始等

###Activity的四种状态
1. Active/Runing ：一个新 Activity 启动入栈后，它在屏幕最前端，处于栈的最顶端，此时它处于可见并可和用户交互的激活状态。 

2. Paused： 当 Activity 被另一个透明或者 Dialog 样式的 Activity覆盖时的状态。此时它依然与窗口管理器保持连接，系统继续维护其内部状态，所以它仍然可见，但它已经失去了焦点故不可与用户交互。 

3. Stoped： 当Activity 被另外一个 Activity 覆盖、失去焦点并不可见时处于 Stoped 状态。 

4. Killed： 当Activity被系统杀死回收或者没有被启动时处于Killed状态。



###Activity生命周期流程图：

![Alt text]({{ BASE_PATH }}/images/activity_lifecycle.png "Activity生命周期")

###Activity生命周期描述

1. 启动Activity：系统会先调用onCreate方法，然后调用onStart方法，最后调用onResume，Activity进入运行状态。

2. 当前Activity被其他Activity覆盖其上或被锁屏：系统会调用onPause方法，暂停当前Activity的执行。

3. 当前Activity由被覆盖状态回到前台或解锁屏：系统会调用onResume方法，再次进入运行状态。

4. 当前Activity转到新的Activity界面或按Home键回到主屏，自身退居后台：系统会先调用onPause方法，然后调用onStop方法，进入停滞状态。

5. 用户后退回到此Activity：系统会先调用onRestart方法，然后调用onStart方法，最后调用onResume方法，再次进入运行状态。

6. 当前Activity处于被覆盖状态或者后台不可见状态，即第2步和第4步，系统内存不足，杀死当前Activity，而后用户退回当前Activity：再次调用onCreate方法、onStart方法、onResume方法，进入运行状态。

7. 用户退出当前Activity：系统先调用onPause方法，然后调用onStop方法，最后调用onDestory方法，结束当前Activity。

{% highlight java %}

package cn.liuyiyou.android.activity;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;


public class LifecycleActivity extends Activity {

    //Activity创建时被调用
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        Log.v("onCreate()","1");
        super.onCreate(savedInstanceState);
        setContentView(R.layout.lifecycle);
    }

    //Activity创建或者从后台重新回到前台时被调用
    @Override
    protected void onStart() {
        Log.v("onStart()","2");
        super.onStart();
    }


    //Activity创建或者从被覆盖、后台重新回到前台时被调用
    @Override
    protected void onResume() {
        Log.v("onResume()","3");
        super.onResume();
    }



    //Activity被覆盖到下面或者锁屏时被调用,或者点击返回的时候
    @Override
    protected void onPause() {
        Log.v("onPause()","4");
        super.onPause();
    }


    //退出当前Activity或者跳转到新Activity时被调用
    @Override
    protected void onStop() {
        Log.v("onStop()","5");
        super.onStop();
    }

    //退出当前Activity时被调用,调用之后Activity就结束了
    @Override
    protected void onDestroy() {
        Log.v("onDestroy()","6");
        super.onDestroy();
    }

    //Activity从后台重新回到前台时被调用
    @Override
    protected void onRestart() {
        Log.v("onRestart()","7");
        super.onRestart();
    }


    /**
     * Activity被系统杀死时被调用.
     * 例如:屏幕方向改变时,Activity被销毁再重建;当前Activity处于后台,系统资源紧张将其杀死.
     * 另外,当跳转到其他Activity或者按Home键回到主屏时该方法也会被调用,系统是为了保存当前View组件的状态.
     * 在onPause之前被调用.
     */
    @Override
    protected void onSaveInstanceState(Bundle outState) {
        Log.v("onSaveInstanceState()","8");
        super.onSaveInstanceState(outState);
    }


    /**
     * Activity被系统杀死后再重建时被调用.
     * 例如:屏幕方向改变时,Activity被销毁再重建;当前Activity处于后台,系统资源紧张将其杀死,用户又启动该Activity.
     * 这两种情况下onRestoreInstanceState都会被调用,在onStart之后.
     */
    @Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        Log.v("onRestoreInstanceState()","9");
        super.onRestoreInstanceState(savedInstanceState);
    }

}
{% endhighlight %}





