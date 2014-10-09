---
layout: post
title:  SpringMVC详细环境搭建
date:   2013/07/01 00:00:00 
category: Spring
tag: Spring mvc
---

##说明

之前是放在有道云笔记上的，不过经过我的反复折腾，不断的在有道和未知笔记上换来换去，删删改改，在今天搭建Velocity环境的适合发现竟然找不到了，所以，干脆写一篇放在博客上，同时在有道云笔记上方一个副本，因为没有用未知笔记了（Mac上有点不给力，当然有道也不给力，连清单待办事项都没有）

之前写了5篇，现在综合成一篇，可能会有点长，对应的代码可能放在github上（mySpring或者SpringMvc项目上）

##最简单的环境

##日志环境

步骤：
    1：使用slf4j和log4j替代common-logging
            1.1：在pom.xml中，加入相应的包，
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${org.slf4j-version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>${org.slf4j-version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${org.slf4j-version}</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.16</version>
            <scope>runtime</scope>
        </dependency>
                1.2：并将common-logging排除。主要是在spring-context中。
       <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${org.springframework-version}</version>
            <exclusions>
                <!-- Exclude Commons Logging in favor of SLF4j -->
                <!-- 使用 slf4j替代commons-logging -->
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
               1.3：在web.xml中，加入log4j的Listener
    <listener>
        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
    </listener>
        
    2：自定义配置文件的存放位置
          Spring配置文件applicationContext.xml和dispatch-servlet.xml默认都是放在WEB-INF下面。而且文件名都有一定限制不能修改。如果想自定义文件名和文件位置，则需要做如下配置：
        2.1：自定义applicatonContext.xml存放位置：
        在web.xml中：
            <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            classpath:applicationContext*.xml
        </param-value>
    </context-param>
        2.2：自定义dispatchservlet.xml存放位置：
        在web.xml中：
        <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:dispatcher-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
        2.3：自定义log4j.properties存放位置：
        在web.xml中：
    <context-param>  
          <param-name>log4jConfigLocation</param-name>  
           <param-value>classpath:log4j.properties</param-value>  
    </context-param> 
                
    3：项目默认字符集设置-即在request请求中，默认使用utf-8接收字符串参数的值
        在web.xml中：
        <filter>
        <filter-name>characterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    4：加入单元测试以及Spring测试
        4.1:加入单元测试：
            在pom.xml中：
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.7</version>
            <scope>test</scope>
        </dependency>
    4.2:加入Spring测试：
        在pom.xml中：
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${org.springframework-version}</version>
            <scope>test</scope>
        </dependency>



不包含任何内容，只是单纯的一个Spring –mvc 程序。则只需要在pom.xml中配置如下即可：

还需要配置仓库。本地活在私服活在公共库都行。

<dependency>

        <groupId>org.springframework</groupId>

        <artifactId>spring-webmvc</artifactId>

        <version>${spring.version}</version>

        <type>jar</type>

        <scope>compile</scope>

      </dependency>

 

<build>

      <plugins>

        <plugin>

           <groupId>org.mortbay.jetty</groupId>

           <artifactId>maven-jetty-plugin</artifactId>

           <version>6.1.10</version>

        </plugin>

      </plugins>

      <finalName>springmvc</finalName>

   </build>

 

这样的情况下，启动jetty:run。即可访问。

这是最简单Spring mvc配置。其中的依赖Maven会自动依赖。查看包的时候，可以看到以下包已经被包含进去：



其中commons-pool.jar 和commons-dbcp是没有的。

 

如果要使用json-lib 。

则需要在spring-servlet.xml中配置

<!-- 需要json-lib支持 <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">

      <property name="messageConverters"> <list> <bean class="org.springframework.http.converter.StringHttpMessageConverter">

      <property name="supportedMediaTypes"> <list> <value>text/html;charset=UTF-8</value>

      </list> </property> </bean> <ref bean="mappingJacksonHttpMessageConverter"

      /><!-- json转换器 </list> </property> </bean> -->

   <!-- <bean id="mappingJacksonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"

      /> -->

集成hibernate验证的时候，加入

<!-- JSR 303 with Hibernate Validator -->

      <dependency>

        <groupId>javax.validation</groupId>

        <artifactId>validation-api</artifactId>

        <version>1.0.0.GA</version>

      </dependency>

      <dependency>

        <groupId>org.hibernate</groupId>

        <artifactId>hibernate-validator</artifactId>

        <version>4.1.0.Final</version>

      </dependency>

 

之后，除了加入这两个包之外，还会加入slf4j-api.jar。因为hibernate使用的是这个日志框架。其中javax.validation的validation-api.jar是一个api。而hibernate-validator是这个api的实现。

要想能启动，则需要加入slf4j-api.jar的实现。我们使用log4j实现

完全方式：

<dependency>

        <groupId>org.slf4j</groupId>

        <artifactId>slf4j-api</artifactId>

        <version>${org.slf4j.version}</version>

      </dependency>

      <dependency>

        <groupId>org.slf4j</groupId>

        <artifactId>jcl-over-slf4j</artifactId>

        <version>${org.slf4j.version}</version>

        <scope>runtime</scope>

      </dependency>

      <dependency>

        <groupId>org.slf4j</groupId>

        <artifactId>slf4j-log4j12</artifactId>

        <version>${org.slf4j.version}</version>

        <scope>runtime</scope>

      </dependency>

      <dependency>

        <groupId>log4j</groupId>

        <artifactId>log4j</artifactId>

        <version>1.2.16</version>

        <scope>runtime</scope>

      </dependency>

 

当然也可以用简写方式，让Maven自动添加

<!-- Logging -->

     

      <dependency>

        <groupId>org.slf4j</groupId>

        <artifactId>slf4j-log4j12</artifactId>

        <version>${org.slf4j.version}</version>

        <scope>runtime</scope>

      </dependency>

     

加入日志框架之后，则会发现，控制台显示的方式比之前好看多了。



 

 

 

      一直以来，以为Spring3.0的版本和3.1的版本差别不大，但是今天在练习Spring MVC的时候发现一个问题：即3.0.5和3.1的Spring mvc有区别。

      在3.0.5中，如果在控制器中的@RequestMapping()中不显示声明映射地址，AbstractUrlHandlerMapping进行处理。

    

2013-06-12 00:10:31,913  INFO [main] (AbstractUrlHandlerMapping.java:411) - Mapped URL path [/account] onto handler 'accountController'

2013-06-12 00:10:31,914  INFO [main] (AbstractUrlHandlerMapping.java:411) - Mapped URL path [/account.*] onto handler 'accountController'

2013-06-12 00:10:31,914  INFO [main] (AbstractUrlHandlerMapping.java:411) - Mapped URL path [/account/] onto handler 'accountController'

 

     而在3.1中。则是使用AbstractHandlerMethodMapping进行处理

2013-06-12 00:12:39,953  INFO [main] (AbstractHandlerMethodMapping.java:186) - Mapped "{[/account],methods=[POST],params=[],headers=[],consumes=[],produces=[],custom=[]}" onto public java.lang.String cn.lyy.controller.AccountController.create(cn.lyy.domain.Account,org.springframework.validation.BindingResult)

2013-06-12 00:12:39,954  INFO [main] (AbstractHandlerMethodMapping.java:186) - Mapped "{[/account/{id}],methods=[GET],params=[],headers=[],consumes=[],produces=[],custom=[]}" onto public java.lang.String cn.lyy.controller.AccountController.getView(java.lang.Long,org.springframework.ui.Model)

2013-06-12 00:12:39,954  INFO [main] (AbstractHandlerMethodMapping.java:186) - Mapped "{[/account],methods=[GET],params=[],headers=[],consumes=[],produces=[],custom=[]}" onto public java.lang.String cn.lyy.controller.AccountController.getCreateForm(org.springframework.ui.Model)

2013-06-12 00:12:39,955  INFO [main] (AbstractHandlerMethodMapping.java:186) - Mapped "{[/test.htm],methods=[],params=[],headers=[],consumes=[],produces=[],custom=[]}" onto public java.lang.String cn.lyy.controller.MyController.test()

 

注意Spring的版本和控制器。

 

学到的东西：

1：Spring mvc 的最简依赖设置。具体可参考pom.xml。其实也不是最简。因为加入了验证。

2：validator验证以及显示。

3：java.util.concurrent.atomic.AtomicLong

这个以前没使用过。

4：国际化

5：Spring 表单验证。

 

5：总结
    按照上述步骤，可以完成目标。不过有一个问题，就是在配置log4j的监听器和文件位置的时候发现不能使用快捷键进行格式化。但是之前ContextLoadListener的时候却可以。
    到目前为止，可以自定义配置文件的名称以及存放位置。定义了字符集，用来解决一般的乱码问题。可以使用junit和Spring-test进行单元测试。

接下来，这是解决静态资源

        

##restful环境

##数据库配置

##json配置

