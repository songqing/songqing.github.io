---
layout: post
category : Android
date: 2014/06/02 08:00:52 
title: 安卓抽屉布局
tags : Android
---
{% include JB/setup %}

##提示，下面的在页面会显示错误。应该是插件自动补全的原因

###概述
抽屉布局(drawerLayout)是在support.v4中新增的布局方式，类似于抽屉，像有道云笔记iOS版本，知乎android版本，印象笔记android版本都有采用该种布局方式。该种布局方式针对大屏的情况比较好，因为是使用滑动的方式，滑出菜单。


###实现方式
1. 需要加入support-v4的依赖包


###要注意这几个原则：
1. 主view(content view) 必须是drawerlayout的第一个子节点
2. 主view宽和高必需match_parent
3. drawer view必须指定android:layout_gravity属性，如果值为“start”，会根据系统的语言顺序自动决定从左滑还是从右滑
4. drawer view宽度不能太宽，最好不要超过320dp

<pre class="brush: xml;">



</pre>









