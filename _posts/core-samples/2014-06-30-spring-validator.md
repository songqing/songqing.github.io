---
layout: post
title:  "Spring验证"
date:   2014/06/30 14:17:14 
category: Spring
tag: [spring,validator]
---

1：验证需要注意的：
   
- 1：前台使用ajax或者表单提交都要验证到。

-  2：前端能友好的显示错误。

-  3: 如果使用单元测试，需要注意将validator对象注入，因为在mvc层面，加载<mvc:annotation-driver>的时候会自动注入validator。


Spring支持Bean Validation1.0(JSR-303)和Bean Validation1.1(JSR-349)，为了保持向前兼容，同时支持Spring validator接口。

一个应用程序可以选择启用一次全局Bean验证

6.2：使用Spring Validator接口进行验证
    6.2.1：定义实体类 Person
    6.2.2：定义实体验证类并实现Validator接口 PersonValidator

Spring3介绍了几种验证方式，303全部被支持，当使用编程方式的时候Spring DataBinder在绑定实体的时候进行验证。3：Spring MVC直接在Controler上进行验证。


1：验证位置

- 1.1：前端 

- 1.2：服务器端


2：表单提交方式

- 2.1：直接使用form表单提交。--这种提交方式没有使用前端验证，需要服务端进行多重验证而且无法将验证的错误漂亮的返回前端。所以不可取，但是却是现在的项目中最常使用的。

- 2.2：使用Spring form表单提交。--这种提交方式和上面一种提交方式类似，但是前端能友好的显示错误信息，但是需要所有的表单都使用Spring form标签。

- 2.3：jQuery form表单提交。，前端jsp页面中不需要使用spring form标签
 
- 2.4：Ajax提交。--这种提交方式，前端jsp页面中不需要使用spring form标签，但是

3：前端验证方式
-    3.1：使用jQuery.validator

4：服务端验证方式

- 4.1：spring自带验证。这种验证方式需要为每个表单编写一个验证类，并实现validator接口。
- 4.2：java validator。这种验证方式需要表单中的name和实体属性相同。
- 4.3：自定义验证方式。


综合：
使用spring form提交和jQuery form 以及ajax提交
服务端最好采用java validator验证，如果之前jsp中定义的name和实体中的不同，则采用自定义的验证方式。





1：一般来说，web项目的验证分为前端和服务器端。前端一般使用jQuery来进行一些非空，字段大小，输入规格等。
但是前端验证后服务器端也需要进行验证。


