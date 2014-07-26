---
layout: post
title:  "iacbook air彻底删除文件以及dea关于属性文件中文乱码"
date:   2014/07/26 19:23:14 
category: 工具使用
---

今天在使用idea编写代码的时候，在属性文件里面需要写中文，发现和eclipse中的不同，在eclipse中书写中文的时候，当回车的时候会自动转换成unicode编码，可是在idea中，.properties文件是中文的。这样在前台显示的时候出现了中文乱码。

刚开始以为是那个.properties插件的问题，就把那个插件禁用，这下可惨了，所有的properties文件都不高亮了，而且无法知道该属性是否在代码中被使用了（这一点ieda很牛逼，知道你哪个地方使用了属性文件里面的某个属性），只是已txt文件查看，而且无法恢复(比如xml文件make成txt文件之后有make as xml，这个却没有)



于是删除了idea，但是删除后重装，依然还是保留了默认设置，问如何恢复默认设置，网上竟然没有找到。最后找到了详细删除的方法：
怎样删除文件

1：直接在finder中移动到废纸篓，如果有残留文件，则先显示隐藏文件（defaults write com.apple.finder AppleShowAllFiles -bool true）

2：然后在资源库中找到想要删除的程序进行删除，一般都是在caches,logs preference  ,application support中，一般情况下都是当前用户所在目录中的资源库。如果是超级用户则是另外的。

重装后，属性文件默认打开方式又回到了properties插件打开的方式，于是继续寻找解决中文乱码的方式，最后在某个不起眼的角落找到了方法：

解决方案：setting->File Encodings 的最下面有一句话："Default encoding for properties files"，选择utf-8,并且在下面的单选框中选中，这样虽然在idea中显示的是中文，但是如果使用notepad打开的时候会显示utf-8的格式。
