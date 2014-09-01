---
layout: post
category : Spring
date: 2014/07/30 01:56:52 
title: Spring的MVC流程
tags : spring
---


###目标：
- 了解Spring-MVC的流程
- 了解几种控制器

###Spring-MVC的好处
- 命令对象基于pojo。不直接依赖Servlet，与web容器解耦，方便单元测试（主要是web测试）
- 清晰的角色划分：前端控制器，请求到处理器映射，处理器适配器，视图解析器，处理器或者页面或者页面控制器，验证器，命令对象，表单对象
- 分工明确，而且扩展点相当灵活


###Spring-MVC核心流程（doDispathch）
- 用户发送请求到前度控制器DispatchServlet，前端控制器收到请求后不进行处理，而是委托给其他的解析器进行处理，作为统一访问点，进行全局流程控制

- DispathchServlet—》HandlerMapping，HandlerMapping将会把会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器对象，多个HandlerInterceptor拦截器）对象，通过这种策略模式，很容易添加新的映射策略。核心代码： mapperHandler = getHander(processRequest,false);

- DispathchServlet —》HandlerAdapter，HanderAdapter将会把处理器包装为适配器。核心代码：HadlerAdapter ha = geetHandlerAdapter(mapperHandler.getHander()); 从而支持多种类型的处理器

- HandlerAdapter—》处理器功能方法的调用，HandlerAdapter将会根据适配的结果调用真正的处理器功能处理方法，完成功能处理，并返回一个ModelAndView对象（包含模型数据和一个逻辑视图）核心代码：mv = ha.handler(processRequest,response,mappingHandler.getHander())

- ModelAndView的逻辑视图名—》ViewResolver，ViewResolver将吧逻辑视图名解析为具体的View，View会根据传过来的的模型数据进行渲染，此处的Model实际是一个map结构。通过这种策略模式很容易支持其他视图技术。核心代码  render(mv, processRequest,response).
返回授权给DispatchServlet, 由DispatchServlet返回给响应用户。

##流程图
![Alt text]({{ BASE_PATH }}/assets/images/spring-mvc.png "SpringMVC流程")



###Spring-MVC涉及到的主要组件
- HandlerMapping  处理映射器
- HandlerAdapter  处理器适配器，并调用处理器相关方法
- HandlerExceptionResolver 异常处理器
- ViewResolver  视图处理器
- LocalResolver  本地化解析器
- ThemeResolver  主题解析器
- MutipartResolver  文件上传解析器
- FlashMapManager

DispatcherServlet默认使用WebApplicationContext作为上下文，因此我们来看一下该上下文中有哪些特殊的Bean：

- 1、Controller：处理器/页面控制器，做的是MVC中的C的事情，但控制逻辑转移到前端控制器了，用于对请求进行处理；

- 2、HandlerMapping：请求到处理器的映射，如果映射成功返回一个HandlerExecutionChain对象（包含一个Handler处理器（页面控制器）对象、多个HandlerInterceptor拦截器）对象；如BeanNameUrlHandlerMapping将URL与Bean名字映射，映射成功的Bean就是此处的处理器；

- 3、HandlerAdapter：HandlerAdapter将会把处理器包装为适配器，并调用处理器相关方法。从而支持多种类型的处理器，即适配器设计模式的应用，从而很容易支持很多类型的处理器；如SimpleControllerHandlerAdapter将对实现了Controller接口的Bean进行适配，并且到处理器的handleRequest方法进行功能处理；

- 4、ViewResolver：ViewResolver将把逻辑视图名解析为具体的View，通过这种策略模式，很容易更换其他视图技术；如InternalResourceViewResolver将逻辑视图名映射为jsp视图；

- 5、LocalResover：本地化解析，因为Spring支持国际化，因此LocalResover解析客户端的Locale信息从而方便进行国际化；

- 6、ThemeResovler：主题解析，通过它来实现一个页面多套风格，即常见的类似于软件皮肤效果；

- 7、MultipartResolver：文件上传解析，用于支持文件上传；

- 8、HandlerExceptionResolver：处理器异常解析，可以将异常映射到相应的统一错误界面，从而显示用户友好的界面（而不是给用户看到具体的错误信息）；

- 9、RequestToViewNameTranslator：当处理器没有返回逻辑视图名等相关信息时，自动将请求URL映射为逻辑视图名；

- 10、FlashMapManager：用于管理FlashMap的策略接口，FlashMap用于存储一个请求的输出，当进入另一个请求时作为该请求的输入，通常用于重定向场景，后边会细述

我们在使用Spring-mvc 的时候，只需要很少的配置，就可以运行一个web层序的原因是，在DispatchServlet中定义了一套默认的组件实现类，即如果你不配置相关组件，Spring会有一套默认的组件，默认组件详细内容在org.springframework.web.servlet.jar的DispatchServlet.properties

下面，我使用一个最简单Spring-mvc程序来分析Spring-mvc的流程，尽量做到配置最少，为了更加了解运行过程，不使用注解，而是使用2.5时候的方式。

一般的过程是：

    -在web.xml中配置DispatchServlet
    HandlerMapping配置请求到处理器的映射
    HandlerAdapter配置，从而支持多种类型的处理器
    ViewResolver配置，从而将逻辑视图名解析为具体视图技术
    处理器配置：即Controler配置

其中，HandlerMapping,HandlerAdapter,ViewResolver都有一个默认的实现，刚开始，我们不配置，所以我们只需要配置DispatchServlet和处理器

在web.xml中：

<pre class="brush: xml;">

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

</pre>

在spring-web.xml中：

<pre class="brush: xml;">

<bean name="/test" class="cn.liuyiyou.spring.simple.web.ControllerTest"></bean>
</pre>
因为默认的映射器是BeanNameUrlHandlerMapping。该类会查找和请求同名的bean处理器.所以需要配置一个bean name为test的处理器。



在处理器中：

<pre class="brush: java;">

public class ControllerTest implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {


        ModelAndView mv = new ModelAndView();
        mv.setViewName("test.jsp");
        return mv;
    }
}
</pre>

默认使用的InternalResourceViewResolver会将逻辑视图名解析为具体的视图。该类使用的是jsp视图。默认情况下前缀是webapp。后缀没有。所以mv.setViewName(“test.jsp”),对应的视图是webapp/test.jsp。

通过debug可以看出流程：

> //初始化mvc开始
> FrameworkServlet 'dispatcher': initialization started 

> BeanDefinitionParserDelegate]No XML 'id' specified - using '/test' as bean name and [] as aliases

>//是否有文件文件上传类型，显然没有
Unable to locate MultipartResolver with name 'multipartResolver': no multipart request handling provided 

>//是否配置了本地化解析，我没有配置，使用默认的
Unable to locate LocaleResolver with name 'localeResolver': using default [org.springframework.web.servlet.i18n.AcceptHeaderLocaleRe

>//是否用到了主题解析器，我没有配置，使用默认的
Unable to locate ThemeResolver with name 'themeResolver': using default [org.springframework.web.servlet.theme.FixedThemeResolver

>//
Creating instance of bean 'org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping'
Mapped URL path [/test] onto handler '/test’
Finished creating instance of bean ‘org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping'

>Creating instance of bean ‘org.springframework.web.servlet.mvc.annotation.
DefaultAnnotationHandlerMapping

>Looking for URL mappings in application context: WebApplicationContext for namespace 'dispatcher-servlet


>Creating instance of bean 'org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter'
'org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter'

>[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter'
[org.springframework.web.servlet.DispatcherServlet]No HandlerAdapters found in servlet 'dispatcher': using default
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerExceptionResolver'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Finished creating instance of bean 'org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerExceptionResolver'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Finished creating instance of bean 'org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Finished creating instance of bean 'org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver'
[org.springframework.web.servlet.DispatcherServlet]No HandlerExceptionResolvers found in servlet 'dispatcher': using default
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Finished creating instance of bean 'org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator'
[org.springframework.web.servlet.DispatcherServlet]Unable to locate RequestToViewNameTranslator with name 'viewNameTranslator': using default [org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator@2a7e471a]
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.view.InternalResourceViewResolver'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Finished creating instance of bean 'org.springframework.web.servlet.view.InternalResourceViewResolver'
[org.springframework.web.servlet.DispatcherServlet]No ViewResolvers found in servlet 'dispatcher': using default
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Creating instance of bean 'org.springframework.web.servlet.support.SessionFlashMapManager'
[org.springframework.beans.factory.support.DefaultListableBeanFactory]Finished creating instance of bean 'org.springframework.web.servlet.support.SessionFlashMapManager'
[org.springframework.web.servlet.DispatcherServlet]Unable to locate FlashMapManager with name 'flashMapManager': using default [org.springframework.web.servlet.support.SessionFlashMapManager@2426c40c]

>[org.springframework.web.context.support.XmlWebApplicationContext]Bean factory for WebApplicationContext for namespace 'dispatcher-servlet': org.springframework.beans.factory.support.DefaultListableBeanFactory@7502b5ad: defining beans [mvcContentNegotiationManager,org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping#0,
org.springframework.format.support.FormattingConversionServiceFactoryBean#0,
org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter#0,
org.springframework.web.servlet.handler.MappedInterceptor#0,
org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExceptionResolver#0,org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver#0,
org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver#0,
org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,
org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,
org.springframework.web.servlet.resource.ResourceHttpRequestHandler#0,
org.springframework.web.servlet.handler.SimpleUrlHandlerMapping#0,
org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter#0,
mappingJacksonHttpMessageConverter,
validatorController,
org.springframework.context.annotation.internalConfigurationAnnotationProcessor,
org.springframework.context.annotation.internalAutowiredAnnotationProcessor,
org.springframework.context.annotation.internalRequiredAnnotationProcessor
,org.springframework.context.annotation.internalCommonAnnotationProcessor,
org.springframework.context.annotation.internalPersistenceAnnotationProcessor,
jstlViewResolver]; parent: org.springframework.beans.factory.support.DefaultListableBeanFactory@5da9a418


###资料：
Spring深度历险：
http://www.iteye.com/blogs/subjects/springmvc-explore
跟开涛学Spring-mvc
http://www.iteye.com/blogs/subjects/kaitao-springmvc

