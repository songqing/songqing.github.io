---
layout: post
category : Spring
date: 2014/07/30 00:56:52 
title: Spring的Mapping和Adapter的原理
tags : spring
---


###概述

该项目通过运行一个最简单的Spring-mvc项目，来分析Spring-mvc的流程，尽量做到配置最少，为了更加了解运行过程，不使用注解，而是使用2.5时候的方式。

之前用过了无数次Spring-mvc也了解过其中的运行原理，但是都是看别人的代码，自己只是偶尔浏览偶尔记忆，但是别人的始终是别人的，不自己试一遍终究了解得不深刻，也许别人某个角落里面的一句话你会常常忽略，直到解决了某个困惑了自己很久的问题才发现原来别人已经说了，只是你没有看到。所以我自己也分析了一下

###一般的过程是：
- 在web.xml中配置DispatchServlet
- HandlerMapping配置请求到处理器的映射
- HandlerAdapter配置，从而支持多种类型的处理器
- ViewResolver配置，从而将逻辑视图名解析为具体视图技术
- 处理器配置：即Controler配置

其中，HandlerMapping,HandlerAdapter,ViewResolver都使用默认的实现，刚开始，我们不配置，来看看默认实现


###配置如下
在web.xml中配置DispatchServlet

{% highlight xml %}    
        <servlet>
            <servlet-name>dispatcher</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:spring-web.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatcher</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>

{% endhighlight %}

处理器或者控制器：

{% highlight java %}    

    public class ControllerTest implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {


        ModelAndView mv = new ModelAndView();
        //因为使用的是默认的InternalResouceResolver，所以没有前缀和后缀，所以对应的是对应webapp/test.jsp。
        mv.setViewName("test.jsp");
        return mv;
    }
}

{% endhighlight %}

spring-web.xml

{% highlight xml %}    

    <bean name="/test" class="cn.liuyiyou.spring.simple.web.ControllerTest"></bean>

{% endhighlight %}

这样的情况下，访问/simple/test即可访问。因为默认使用了BeanNameUrlMapping和SimpleControllerAdpter。所以配置了：

{% highlight xml %}    

    <bean name="/test" class="cn.liuyiyou.spring.simple.web.ControllerTest"></bean>

{% endhighlight %}

的时候，当访问一个/test时，会映射到处理器  ControllerTest。而SimpleControlerAdapter则会适配实现了Controller接口的类，并使用其中的handleRequest方法来处理业务逻辑

来做一个实验，之前在使用注解的时候都是使用

{% highlight xml %}    
    <mvc-annotation-driver />
{% endhighlight %}

来实现，而且在控制器中需要在类上使用@Controller并且在方法中使用@RequestMapping。

我们不这样使用，而是使用BeanNameUrlMapping和AnnotationMethodHandlerAdapter这种奇葩的组合方式。

{% highlight xml %}    

    <bean name="/test2" class="cn.liuyiyou.spring.simple.web.ControllerTest2"></bean>

    <bean name="/test3" class="cn.liuyiyou.spring.simple.web.ControllerTest2"></bean>

{% endhighlight %}

并没有使用@Controller注解

{% highlight java %}    


//并没有使用@Controller注解
public class ControllerTest2 {

@RequestMapping(value = "/test2")
public String test2(){
return "test2.jsp";
}


@RequestMapping(value = "/test3")
public String test3(){
return "test3.jsp";
}
}

{% endhighlight %}

debug：

> BeanNameUrlHandlerMapping]Mapped URL path [/test2] onto handler '/test2’
> .DefaultAnnotationHandlerMapping]Rejected bean name '/test2': no URL paths identified


可以看到test2已经映射到处理器了。

可以看到并没有映射到test2.因为没有在上面注解@Controller，而是通过BeanNameUrlHandlerMapping来实现的。而具体的处理方法是通过AnnotationMethodHandlerAdapter适配器来访问的。

当使用了mvc-annotation-driver的时候，使用了默认的DefaultAnnotationMapping和AnnotationAdapter，前者会将请求映射到使用@Controller的处理器，后者将会将注解了@RequestMapping的方法处理业务逻辑，并返回ModelAndView给DispatchServlet来进行渲染，在3.2中，则会使用RequestMethodMapping和RequestMthodAdapter来替代上面的两个类。

