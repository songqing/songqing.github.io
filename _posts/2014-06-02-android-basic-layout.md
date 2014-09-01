---
layout: post
category : Android
date: 2014/06/02 00:56:52 
title: 安卓五大基本布局
tags : Android
---


##提示，下面的在页面会显示错误。应该是插件自动补全的原因

###android的常用布局
1. 线性布局(LinearLayout)
2. 单帧布局(FrameLayout)
3. 绝对布局(AbsoluteLayout)
4. 相对布局(RelativeLayout)
5. 表格布局(TableLayout)

###线性布局(LinearLayout)
线性布局(LinearLayout)按照垂直或者水平方向的顺序一次排列子元素，每一个子元素都位于前一个元素之后。如果是垂直排列，那么将是一个N行单列的结构，每一行都只有一个元素，而不管这个元素的宽度是多少。如果是水平排列，那么将是一个-行N列的结构。

所以一般情况下，如果想使用线性布局(LinearLayout)来完成多行多列的结构，通常的方式先垂直排列两个元素，每一个元素里面包含一个线性布局(LinearLayout)进行水平排列

###实例
使用线性布局(LinearLayout)来显示一个登录框

<pre class="brush: xml;">


<!--最外面的布局是垂直布局，里面的小LinerLayout是默认的水平布局-->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">


    <!--用户名的Label和输入框-->
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" 
            android:text="UserName"/>
        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:id="@+id/editText3"
            android:layout_weight="1"
            android:text="请输入用户名" />
        
        </LinearLayout>

    <!--密码的Label和输入框-->
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
        
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" 
            android:text="Password"/>
        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPassword"
            android:id="@+id/editText2"
            android:layout_weight="1"
            android:text="请输入密码" />

    </LinearLayout>
    
    <!--登录按钮-->
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
   
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Login"/>

        
    </LinearLayout>

   
</LinearLayout>

</pre>

###单帧布局(FrameLayout)

单帧布局(FrameLayout)中，整个界面被当成一块空白备用区域，所有的子元素不能被指定放置的位置，他们统统位于这块区域的左上角，并且后面的子元素直接覆盖在前面元素的子元素之上，将前面的子元素部分或者全部遮挡。

不过他提供了一个layout:gravity。里面有多个属性（top,bottom left ,right,center,），可以自由组合。可以比较好的组成一个九宫格的模式

###实例：
<pre class="brush: xml;">

<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">


    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText5"
        android:layout_gravity="left|top" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText6"
        android:layout_gravity="center_horizontal|top" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText7"
        android:layout_gravity="right|top" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText8"
        android:layout_gravity="left|center_vertical" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText9"
        android:layout_gravity="center" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText10"
        android:layout_gravity="right|center_vertical" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText11"
        android:layout_gravity="left|bottom" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText12"
        android:layout_gravity="center_horizontal|bottom" />

    <EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inputType="textPersonName"
        android:text="Name"
        android:ems="10"
        android:id="@+id/editText13"
        android:layout_gravity="right|bottom" />
</FrameLayout>


</pre>

###3. 绝对布局(AbsoluteLayout)

绝对布局(AbsoluteLayout)就是指定xy坐标进行布局，这种模式比较少用，因为现在安卓的各种屏幕大小以及各种分辨率的原因，使用该布局比较难以进行调试，不好进行适配,而且在安卓4.1中已经提示该布局已经过时了。

###实例
<pre class="brush: xml;">

<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <TextView android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#ffffffff"
        android:gravity="center"
        android:layout_x="50dp"
        android:layout_y="50dp"
        android:text="1"/>


    <TextView android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#ff654321"
        android:gravity="center"
        android:layout_x="25dp"
        android:layout_y="25dp"
        android:text="2"/>

    <TextView  android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#fffedcba"
        android:gravity="center"
        android:layout_x="125dp"
        android:layout_y="125dp"
        android:text="3"/>
</AbsoluteLayout>

</pre>




###4.相对布局(RelativeLayout)

相对布局(RelativeLayout)按照各个子元素的位置关系完成布局，在此布局中的子元素里与位置相关的属性将生效。



- android:layout_toLeftOf —— 该组件位于引用组件的左方
- android:layout_toRightOf —— 该组件位于引用组件的右方
- android:layout_above —— 该组件位于引用组件的上方
- android:layout_below —— 该组件位于引用组件的下方
- android:layout_alignParentLeft —— 该组件是否对齐父组件的左端
- android:layout_alignParentRight —— 该组件是否齐其父组件的右端
- android:layout_alignParentTop —— 该组件是否对齐父组件的顶部
- android:layout_alignParentBottom —— 该组件是否对齐父组件的底部
- android:layout_centerInParent —— 该组件是否相对于父组件居中
- android:layout_centerHorizontal —— 该组件是否横向居中
- android:layout_centerVertical —— 该组件是否垂直居中


子元素就通过这些属性和各自的ID配合指定位置关系。注意在指定位置关系时，引用的ID必须在引用之前，先被定义，否则将出现异常。

###实例，中间一个，四方个一个

<pre class="brush: xml;">

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <TextView
        android:id="@+id/text_01"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#ffffffff"
        android:gravity="center"
        android:layout_alignParentBottom="true"
        android:text="1" />

    <TextView
        android:id="@+id/text_02"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#ff654321"
        android:gravity="center"
        android:layout_above="@id/text_01"
        android:layout_centerHorizontal="true"
        android:text="2" />

    <TextView
        android:id="@+id/text_03"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:background="#fffedcba"
        android:gravity="center"
        android:layout_toLeftOf="@id/text_02"
        android:layout_above="@id/text_01"
        android:text="3" />
</RelativeLayout>

</pre>


###表格布局(TableLayout)

表格布局(TableLayout)为表格布局，适用于N行N列的布局格式，一个TableLaoyOut由许多TableRow组成。一个TableRow代表TableLayout中的一行。

TableLayout是LinerLayout的子类，它的android:orientation属性恒为horizontal，并且它的android:layout_width和android:layout_height属性恒为match_parent和wrap_content。所以它的子元素都是横向排列。并且是高度一致的。这样的设计使得每个TableRow里面子元素都相当于表格中的单元格一样。单元格可以为空，但是不能跨列。

###实例

<pre class="brush: xml;">


<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">


    <TableRow
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:text="Name"
            android:ems="10"
            android:id="@+id/editText0"
            android:layout_column="0" />

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:text="Name"
            android:ems="10"
            android:id="@+id/editText1"
            android:layout_column="1" />
    </TableRow>

    <TableRow
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:text="Name"
            android:ems="10"
            android:id="@+id/editText2"
            android:layout_column="0" />

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:text="Name"
            android:ems="10"
            android:id="@+id/editText3"
            android:layout_column="1" />
    </TableRow>

    <TableRow
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:text="Name"
            android:id="@+id/editText4"
            android:layout_column="0" />

        <EditText
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inputType="textPersonName"
            android:text="Name"
            android:id="@+id/editText5"
            android:layout_column="1" />
    </TableRow>

</TableLayout>

</pre>









