---
layout: post
title:  SpringMVC详细环境搭建
date:   2013/07/01 00:00:00 
category: Spring
tag: Spring mvc
---

###说明
从这篇开始，将Spring做一个总结。首先是Spring-MVC之前觉得这个很重要，后面发现其实很多流行的框架都实现了mvc模式，换一个框架可能Spring-mvc就用不到了，就像使用webx或者structs就不一定用到SpringMVC，反而Spring的其他地方一般的mvc框架都会支持，不过因为这个用得最久，也用得最熟，所以从mvc开始，再推到其他模块。

其次，之前虽然用到了，但是没有好好总结，所以再次搭建的时候需要去其他地方或者网上找，还不如自己来搜集资料好好总结一下。

之前写了5篇，现在综合成一篇，可能会有点长，对应的代码可能放在github上（SpringMvc项目上）

之前是放在有道云笔记上的，不过经过我的反复折腾，不断的在有道和未知笔记上换来换去，删删改改，在今天搭建Velocity环境的适合发现竟然找不到了，所以，干脆写一篇放在博客上，同时在有道云笔记上方一个副本，因为没有用未知笔记了（Mac上有点不给力，当然有道也不给力，连清单待办事项都没有）

###环境：
- idea
- tomcat
- Maven  

###目的

1. Spring mvc 的最简依赖设置

2. Spring MVC增加日志

3. rest风格以及静态文件的处理

4. 使用velocity视图

5. 配置事务

6. 。。。。。。。


###一：最简单的环境


###目标：
1. 全部使用spring默认设置
2. 运行的时候不报错即可


###步骤：
1. 在```pom.xml```中添加以下依赖

{% highlight xml %}

  <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
    </dependency>


{% endhighlight %}


2. 在```web.xml```设置```DispatcherServlet```

{% highlight xml %}

 <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

{% endhighlight %}

3. 在```dispatcher-servlet```中不需要任何配置，只是要将该文件放到```WEB-INF```下

{% highlight xml %}

  <!-- 里面不需要写什么 -->

{% endhighlight %}


###小结：
这是最简单Spring mvc配置。其中的依赖Maven会自动依赖。查看包的时候，可以看到以下包已经被包含进去,只是这个只能说明配置成功，基本上只能保证运行的时候不报错

使用到的各个组件分别是：


###目标：
1. 可以运行Springmvc程序

###说明


###步骤：
1. 在```pom.xml```中加入以下依赖：因为在控制器中需要用servlet

{% highlight xml %}

       <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>

{% endhighlight %}


2. ```dispatcher-servlet```中做如下配置使用的是Spring默认的```BeanNameUrlMapping```

{% highlight xml %}

  <bean name="/user.do" class="TestController" />

{% endhighlight %}


3. 在```TestController```中实现```Controller```接口

{% highlight java %}

public class TestController  implements Controller {


    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView("user.jsp");
        ModelMap modelMap = new ModelMap();
        modelMap.put("message","this is message");
        mv.addAllObjects(modelMap);
        return mv;
    }
}

{% endhighlight %}

4. ```user.jsp```在webapp下，不是在WEB-INF下

{% highlight html %}

 <html>
 <body>
 ${message}
 </body>
 </html>

{% endhighlight %}

###小结：
这样配置好之后，可以运行Spring-mvc程序了，使用到的各个组件分别是：



###目标：
- 使用slf4j和log4j替代common-logging

###步骤：

1. 在```pom.xml```中增加以下依赖包

{% highlight xml %}


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


      {% endhighlight %}
               

2. 在```pom.xml```中将common-logging排除。主要是在spring-context中。

{% highlight xml %}

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


{% endhighlight %}
        

3. 在```web.xml```中，加入```log4j```的```Listener```


{% highlight xml %}


    <listener>
        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
    </listener>


{% endhighlight %}

###小结
使用以上配置之后，控制台比之前漂亮很多，而且如果开启debug，可以非常详细的看到Spring启动信息，方便调试
        

###目的        

- 自定义配置文件的存放位置

###说明：
Spring配置文件applicationContext.xml和dispatch-servlet.xml默认都是放在WEB-INF下面。而且文件名都有一定限制不能修改。如果想自定义文件名和文件位置，则需要做如下配置：
   
###步骤    

- 1：自定义```applicatonContext.xml```的存放位置：在```web.xml```中：

{% highlight xml %}

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            classpath:applicationContext*.xml
        </param-value>
    </context-param>

{% endhighlight %}

这样配置之后，可以不需要放在指定位置，而且可以分开放


- 2.自定义```dispatchservlet.xml```存放位置：在```web.xml```中

{% highlight xml %}

 <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:dispatcher-servlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

{% endhighlight %}


- 3：自定义log4j.properties存放位置：在```web.xml```中：

{% highlight xml %}

    <context-param>  
          <param-name>log4jConfigLocation</param-name>  
           <param-value>classpath:log4j.properties</param-value>  
    </context-param> 


 {% endhighlight %}          


###小结
这样配置之后，可以随意存放```applicationContex.xml```, ```dispathcher-servelt.xml``` ```log4j.properties```的位置和名称，不过一般都是放在resources下。


下面的还没有修改

- 3：项目默认字符集设置-即在request请求中，默认使用utf-8接收字符串参数的值
        在web.xml中：
    
{% highlight xml %}
    

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

 {% endhighlight %}   


- 4：加入单元测试以及Spring测试
        
- 4.1:加入单元测试：在pom.xml中：
  
{% highlight xml %}
         
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.7</version>
            <scope>test</scope>
        </dependency>

{% endhighlight %} 



    4.2:加入Spring测试：
        在pom.xml中：

{% highlight xml %}
    
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${org.springframework-version}</version>
            <scope>test</scope>
        </dependency>

         {% endhighlight %} 





如果要使用json-lib 。

则需要在spring-servlet.xml中配置

{% highlight xml %}

 <!-- 需要json-lib支持 <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">

      <property name="messageConverters"> <list> <bean class="org.springframework.http.converter.StringHttpMessageConverter">

      <property name="supportedMediaTypes"> <list> <value>text/html;charset=UTF-8</value>

      </list> </property> </bean> <ref bean="mappingJacksonHttpMessageConverter"

      /><!-- json转换器 </list> </property> </bean> -->

   <!-- <bean id="mappingJacksonHttpMessageConverter" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"

      /> -->

{% endhighlight %} 

集成hibernate验证的时候，加入

{% highlight xml %}

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

 {% endhighlight %} 

之后，除了加入这两个包之外，还会加入slf4j-api.jar。因为hibernate使用的是这个日志框架。其中javax.validation的validation-api.jar是一个api。而hibernate-validator是这个api的实现。

要想能启动，则需要加入slf4j-api.jar的实现。我们使用log4j实现

完全方式：

{% highlight xml %}

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

 
 {% endhighlight %} 

当然也可以用简写方式，让Maven自动添加

{% highlight xml %}

 <!-- Logging -->

     <dependency>

        <groupId>org.slf4j</groupId>

        <artifactId>slf4j-log4j12</artifactId>

        <version>${org.slf4j.version}</version>

        <scope>runtime</scope>

      </dependency>

 {% endhighlight %} 
     

加入日志框架之后，则会发现，控制台显示的方式比之前好看多了。



注意Spring的版本和控制器。


###总的pom.xml：
 

{% highlight xml %}


 <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <org.springframework-version>3.2.0.RELEASE</org.springframework-version>
        <org.slf4j-version>1.5.10</org.slf4j-version>
        <org.aspectj-version>1.6.8</org.aspectj-version>
        <org.mybatis.pagination.version>0.0.3</org.mybatis.pagination.version>
        <commons.lang3.version>3.1</commons.lang3.version>
        <sitemesh.version>2.4.2</sitemesh.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>15.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${org.springframework-version}</version>
            <exclusions>
                <!-- Exclude Commons Logging in favor of SLF4j -->
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib-nodep</artifactId>
            <version>2.2</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>



        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>
        <!-- Logging -->
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

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.18</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <!-- 增加这个包，则会自动引入commons-pool -->
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.4</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>


        <!-- 增加这个包，则会自动引入spring-jdbc,spring-web,spring-tx -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${org.springframework-version}</version>
            <type>jar</type>
        </dependency>


<!--velocity 需要这个的支持-->

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${org.springframework-version}</version>
            <type>jar</type>
        </dependency>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>${org.aspectj-version}</version>
        </dependency>


        <!-- JSR 303 with Hibernate Validator -->
        <dependency>
            <groupId>javax.validation</groupId>
            <artifactId>validation-api</artifactId>
            <version>1.0.0.GA</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator-annotation-processor</artifactId>
            <version>4.1.0.Final</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.2.4</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.2.2</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>1.0.2.RELEASE</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.7</version>
            <type>jar</type>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>0.2.9</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.1.26</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.7.1</version>
        </dependency>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.18</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-core-asl</artifactId>
            <version>1.9.3</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-mapper-asl</artifactId>
            <version>1.9.3</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.2.2</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>${commons.lang3.version}</version>
        </dependency>
        <dependency>
            <groupId>commons-beanutils</groupId>
            <artifactId>commons-beanutils</artifactId>
            <version>1.8.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-core</artifactId>
            <version>3.5.0</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-highlighter</artifactId>
            <version>3.5.0</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-core</artifactId>
            <version>1.2.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-ehcache</artifactId>
            <version>1.2.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-web</artifactId>
            <version>1.2.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.2.2</version>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>net.databinder.dispatch</groupId>
            <artifactId>jsoup_2.8.1</artifactId>
            <version>0.9.1</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>

        <!-- jpa start -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>4.0.0.Final</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.hibernate.javax.persistence</groupId>
            <artifactId>hibernate-jpa-2.0-api</artifactId>
            <version>1.0.1.Final</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>4.0.0.Final</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <!-- jpa end -->


        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongo-java-driver</artifactId>
            <version>2.10.1</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>commons-codec</groupId>
            <artifactId>commons-codec</artifactId>
            <version>1.7</version>
        </dependency>

        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20080701</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>
        <!-- sitemesh -->
        <dependency>
            <groupId>opensymphony</groupId>
            <artifactId>sitemesh</artifactId>
            <version>${sitemesh.version}</version>
            <scope>runtime</scope>
        </dependency>


        <dependency>
            <groupId>org.hsqldb</groupId>
            <artifactId>hsqldb</artifactId>
            <version>1.8.0.10</version>
        </dependency>


       <dependency>
           <groupId>org.apache.velocity</groupId>
           <artifactId>velocity</artifactId>
           <version>1.7</version>
       </dependency>


       <dependency>
           <groupId>org.apache.velocity</groupId>
           <artifactId>velocity-tools</artifactId>
           <version>2.0</version>
       </dependency>

       <dependency>
           <groupId>commons-digester</groupId>
           <artifactId>commons-digester</artifactId>
           <version>1.8</version>
       </dependency>

    </dependencies>



{% endhighlight %} 


 



        

