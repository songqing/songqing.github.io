---
layout: post
title:  一个简单的WebX处理路径分析
date:   2014/09/26 00:00:00 
category: webx
tag: alibaba
---

有时候没有耐心看完，先把总结写好：（总结只对应默认情况）

- webx暂时还不支持rest方法，所以，访问的时候需要后缀，比如.htm .do .json

- <code>http://localhost:8081/simple/reponsd_object.do</code> 看上去是不需要模板的，但是如果<code>url</code>的参数没有和<code>ReponseObject#execute()</code>
中的参数匹配的话，会提示找不到模板。

- <code>http://localhost:8081/simple/response_object.do?name=liuyiyou&password=123456&email=liuyiyou@yahoo.com</code>
 已这样的方式访问，会显示， 并执行<code>ReponseObject#execute(.....)</code>方法

- <code>http://localhost:8081/simple/response_object.htm?name=liuyiyou&password=123456&email=liuyiyou@yahoo.com</code>
会提示找不到<code>/screen/simple/responseObject</code>模板

- 不带后缀和后缀名为.htm得到的结果一样，但是.html则不一样，需要.html模板，而且模板名字和访问路径必须一样，webx不会像处理vm模板一样处理大小写（不一定，需要看pipeline.xml中的配置方式）

- .do和.json基本没有什么区别（不一定，需要看pipeline.xml中的配置方式）

- execute()方法可以带参数，但是不能随便带，<code>com.alibaba.citrus.turbine.dataresolve</code>包中注解的可以<code>Navigator</code>和 <code>Context</code>也可以（不一定，需要看webx-component-and-root.xml中的配置方式）

- 一个moduel.scren可以处理多个请求，默认是<code>doPerform</code>,其他的已<code>doXXX</code>开头的都可以，里面的参数可以参考上面这条。 <code>beforeExecution</code> 和<code>afterExecution</code>看名字也知道是在executeion前后处理

- 如果参数里面的参数是<code>Context</code> 一般需要加对应的模板



- .....待续



好了！正文开始

之前在刚开始接触<code>webx</code>的是，发现如果<code>url</code>的后缀名是<code>.htm</code>.h则需要有模板支持。如果是<code>.do</code>.do则不需要

后面发现这句话有点太满，甚至我接下来的写的也有点太满，因为不只是看后缀，还要看如何配置以及底层如何实现的，所以这里只拿webx自带的例子来说明：

第一种情况：不需要模板，前台直接输出，类似于在servlet中直接写html代码。这种情况一般适用于ajax请求或者json，但是实际情况下，一般不这么使用，有点麻烦，后面的有几种方法可以做到类似的效果。

<code>http://localhost:8081/simple/say_hi.do</code>

会首先找到app1（模块名，因为只有一个模块）下的<code>moudel.screen.simple.SayHi</code>.默认执行里面的<code>execute（）</code>方法。没有返回值。所以有点麻烦，需要输出什么都需要在后台通过<code>response.getWriter();</code>来写

{% highlight java %}   


public class ResponseObject {

    @Autowired
    private HttpServletResponse response;


    public void execute()  throws IOException{
        User user = new User();
        user.setName("liuyiyou");
        user.setPassword("12345");
        user.setEmail("liuyiyou@yahoo.com");
        response.getWriter().println(user);
        
    }
}

{% endhighlight %}

<code>http://localhost:8081/simple/reponsd_object.do</code>访问的时候，浏览器会显示user.toString()数据。

但是如果这样写：

{% highlight java %}   

public class ResponseObject {

    @Autowired
    private HttpServletResponse response;


    public User execute()  throws IOException{
        User user = new User();
        user.setName("liuyiyou");
        user.setPassword("12345");
        user.setEmail("liuyiyou@yahoo.com");

        System.out.println("test");
       // response.getWriter().println(user);

        return user;
    }
}

{% endhighlight %}

虽然程序会执行到该方法，但是前台没有任何数据显示，而且前台也不会报错。

这样写：

{% highlight java %}   

public class ResponseObject {

    @Autowired
    private HttpServletResponse response;


    public void execute(String name,int age)  throws IOException{
        User user = new User();
        user.setName("liuyiyou");
        user.setPassword("12345");
        user.setEmail("liuyiyou@yahoo.com");

        System.out.println("test");
        response.getWriter().println(user);
    }
}

{% endhighlight %}

程序报错，所以对于参数是有规定的，具体规定如何，到后面再看。

这样写：

{% highlight java %}   


public class ResponseObject {

    @Autowired
    private HttpServletResponse response;


    public void execute( @Param("name")String name,@Param("password")String password,@Param("email")String email)  throws IOException{
        User user = new User();
        user.setName(name);
        user.setPassword(password);
        user.setEmail(email);

        System.out.println("test");
        response.getWriter().println(user);
    }
}

{% endhighlight %}

不会出错，因为会给一个默认值，前台会打印null，至少从上面两种情况可以知道execute方法里面的参数不是随便写的。

<code>http://localhost:8081/simple/response_object.do?name=liuyiyou&password=123456&email=liuyiyou@yahoo.com</code>
 已这样的方式访问，则前台显示。

<code>http://localhost:8081/simple/response_object.htm?name=liuyiyou&password=123456&email=liuyiyou@yahoo.com</code>
会提示找不到<code>/screen/simple/responseObject</code>模板

所以，只要有使用这个包里面的注解com.alibaba.citrus.turbine.dataresolve的参数，才会显示。后面会复现上面的错误，看是如何处理的。

二：直接使用带有.json的方法：
访问方式如下：<code>http://localhost:8081/multievent/say_hello_2/english.json</code>

程序会找<code>app1.moudule.multienent.SayHello2#doEnglish()</code>方法


{% highlight java %}   

/**
 * Screen方法可带有返回值。
 * 这个screen所返回的对象将被转换成json格式，并输出到用户浏览器。
 *
 */
public class SayHello2 {

    @Autowired
    private HttpServletResponse response;

    /** 英文 */
    public Hello doEnglish() {
        return new Hello("English", "Hello");
    }

    /** 中文 */
    public Hello doChinese() {
        return new Hello("Chinese", "你好");
    }

    /** 法语 */
    public Hello doFrench() {
        return new Hello("French", "Bonjour");
    }

    /** 西班牙语 */
    public Hello doSpanish() {
        return new Hello("Spanish", "Hola");
    }

    public Hello doTest(@Param(name = "param" ,defaultValue = "test")String test) {
        System.out.println("spanish");
        return new Hello("Spanish", "xxxxxx");
    }


    public void doTest() throws IOException {
        response.getWriter().println("Spanish: 无返回值");
    }
}

    
{% endhighlight %}

注意doEnglish()没有参数，如果加参数会如何呢？

{% highlight java %} 

public Hello doTest(String test) {
        return new Hello("Spanish", "Hola");
    }

{% endhighlight %}

会报前面同样的错误.

如果是这样：

{% highlight java %} 


public Hello doTest(@Param(name = "param" ,defaultValue = "test")String test) {
        return new Hello("Spanish", test);
    }

{% endhighlight %}


正常，注意，如果不需要默认值，则只需要@Param(“param”),如果需要，则要使用键值对。注意是name不是value

注意：这种方法，后缀需要是.json

这种情况下：

{% highlight java %} 

 public void doTest() throws IOException {
        response.getWriter().println("Spanish: 无返回值");
    }

{% endhighlight %}

<code>http://localhost:8081/multievent/say_hello_2/test.do</code>

</code>http://localhost:8081/multievent/say_hello_2/test.json</code>

都会访问到，这个可以联想到方法重载，与返回值无关


第三种情况：处理带.do的方法
这个和上面这种类似，


{% highlight java %} 


/**
 * 这个例子演示了用一个screen类处理多个事件的方法。
 *
 * @author Michael Zhou
 */
public class SayHello1 {

    @Autowired
    private HttpServletResponse response;

    /** 此方法会在所有的event handler之前执行。 */
    public void beforeExecution() {
        response.setContentType("text/plain");
    }

    /** 此方法会在所有的event handler之后执行。 */
    public void afterExecution() throws IOException {
        response.flushBuffer(); // 此调用并非必须，只是用来演示afterExecution方法而已
    }

    /** 默认语言：英文 */
    public void doPerform() throws IOException {
        doEnglish();
    }

    /** 英文 */
    public void doEnglish() throws IOException {
        response.getWriter().println("English: Hello");
    }

    /** 中文 */
    public void doChinese() throws IOException {
        response.getWriter().println("Chinese: 你好");
    }

    /** 法语 */
    public void doFrench() throws IOException {
        response.getWriter().println("French: Bonjour");
    }

    /** 西班牙语 */
    public void doSpanish() throws IOException {
        response.getWriter().println("Spanish: Hola");
    }
}


{% endhighlight %}

使用.do和这.json都能访问到

第四种：返回模板数据


{% highlight java %} 

public class SayHello3 {

    @Autowired
    private HttpServletResponse response;

    /** 此方法会在所有的event handler之前执行。 */
    public void beforeExecution() {
        response.setContentType("text/plain");
    }

    /** 此方法会在所有的event handler之后执行。 */
    public void afterExecution() throws IOException {
        response.flushBuffer(); // 此调用并非必须，只是用来演示afterExecution方法而已
    }

    /** 默认语言：英文 */
    public void doPerform(Context context) throws IOException {
        context.put("name","liuyiyou");
        //doEnglish();
    }

   
}

{% endhighlight %}


如果没有模板，则会报错。和SyaHello2比较，发现，只是多了一个Context参数而已。
加入模板之后，访问：http://localhost:8081/multievent/say_hello_3  正常渲染模板



