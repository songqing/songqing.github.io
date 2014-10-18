---
layout: post
title:  Velocity模板加载方式
date:   2014/10/18 00:00:00 
category: velocity
tag: velocity
---

##概述

新项目前端使用的语言不是我一直使用的jsp，而是velocity，不过其实差别不大，只是语法习惯不同罢了，不过velocity的静态化是jsp没有的（jsp也可以实现），所以想来学习一下，没想到，光一个指定静态化文件的模板路径就把我一下搞死了，网上很多资料极度不靠谱。说如果要静态化要引入几个velocity相关文件，结果发现只要一个1.7版本的就可以了。

##总结一下

不管是以何种方式加载模板路径，都是相对于该项目，即与项目平级。如果是一个多模块的maven项目，则使用类加载的时候尤其要注意。比如我是项目是SpringMVC里面有一个velocity模块，我使用类路径加载模板的时候，刚开始使用的是``cn/liuyiyou/spring/velocity/template/user6.vm``发现无法加载到模板。直到使用``velocity/src/main/java/cn/liuyiyou/spring/velocity/template/user6.vm``才可以。其中velocity是我的模块名，如果我的是单独的maven项目，可能就只需要``cn/liuyiyou/spring/velocity/...``但是奇怪的是，我将模板放到``src/main/resources``下又可以得到

###第一种方式：通过文件加载方式（默认方式）


{% highlight java %}

   public static void loderTemplatePathDefault(){
        VelocityEngine velocityEngine = new VelocityEngine();
        VelocityContext velocityContext = new VelocityContext();
        velocityContext.put("name","liuyiyou");
        velocityContext.put("age","18");
        StringWriter writer = new StringWriter();
//        velocityEngine.mergeTemplate("template/user5.vm","utf-8",velocityContext,writer)
        //user.vm在于项目同级下
        Template template =  velocityEngine.getTemplate("template/user2.vm","utf-8");
        template.merge(velocityContext, writer);
        System.out.println(writer.toString());

        StringWriter writer2 = new StringWriter();
        User user = new User();
        user.setName("liuyiyou");
        user.setAge(11);
        velocityContext.put("user",user);
        Template template2 = Velocity.getTemplate("user3.vm");
        template2.merge(velocityContext,writer2);
        System.out.println(writer2.toString());
    }


{% endhighlight %}

## ----------------------------------------------------------------------------

##第二种方式：从类路径加载模板文件


{% highlight java %}


 Properties properties = new Properties();
        properties.setProperty("resource.loader","class");
        properties.setProperty("class.resource.loader.class","org.apache.velocity.runtime.resource.loader.ClasspathResourceLoader");

        VelocityEngine velocityEngine = new VelocityEngine(properties);
        VelocityContext velocityContext = new VelocityContext();
        velocityContext.put("name","liuyiyou");
        velocityContext.put("age","18");
        StringWriter writer = new StringWriter();
        File directory = new File("");
        System.out.println(directory.getCanonicalPath());
        System.out.println(directory.getAbsolutePath());
        //这个在SpringMVC项目下的velocity模块下的src/main/java下的cn.liuyiyou.spring.velocity.template/user5.vm 报错
        Template template =  Velocity.getTemplate("velocity/src/main/java/cn/liuyiyou/spring/velocity/template/user6.vm", "utf-8");
        //这个在SpringMVC项目template文件夹下，与main同级
        //Template template =  Velocity.getTemplate("template/user4.vm", "utf-8");
        template.merge(velocityContext,writer);
        System.out.println(writer.toString());


        StringWriter writer2 = new StringWriter();
        User user = new User();
        user.setName("liuyiyou");
        user.setAge(11);
        velocityContext.put("user",user);
        //在main/resources/user.vm下
        Template template2 =  velocityEngine.getTemplate("user5.vm","utf-8");
        template2.merge(velocityContext,writer2);
        System.out.println(writer2.toString());


{% endhighlight %}

# ----------------------------------------------------------------------------


##第三种方式：从jar文件加载模板路径(这个未验证，不过可以将vm放在另外一个模块下来进行验证)


{% highlight java %}


 Properties properties = new Properties();
        properties.setProperty("resource.loader","jar");
        properties.setProperty(Velocity.FILE_RESOURCE_LOADER_PATH,"jar:file:webapp/WEB-INF/lib/vm.jar");
        VelocityEngine velocityEngine = new VelocityEngine(properties);
        VelocityContext velocityContext = new VelocityContext();
        velocityContext.put("name","liuyiyou");
        velocityContext.put("age","18");
        StringWriter writer = new StringWriter();
        Template template =  velocityEngine.getTemplate("template/user5.vm","utf-8");
        template.merge(velocityContext, writer);
        System.out.println(writer.toString());


{% endhighlight %}


##资料：
[link text][http://www.blogjava.net/sxyx2008/archive/2010/11/11/337799.html] http://www.blogjava.net/sxyx2008/archive/2010/11/11/337799.html



# ----------------------------------------------------------------------------
# R U N T I M E  L O G
# ----------------------------------------------------------------------------

# ----------------------------------------------------------------------------
#  default LogChute to use: default: AvalonLogChute, Log4JLogChute, CommonsLogLogChute, ServletLogChute, JdkLogChute
# ----------------------------------------------------------------------------

runtime.log.logsystem.class = org.apache.velocity.runtime.log.AvalonLogChute,org.apache.velocity.runtime.log.Log4JLogChute,org.apache.velocity.runtime.log.CommonsLogLogChute,org.apache.velocity.runtime.log.ServletLogChute,org.apache.velocity.runtime.log.JdkLogChute

# ---------------------------------------------------------------------------
# This is the location of the Velocity Runtime log.
# ----------------------------------------------------------------------------

runtime.log = velocity.log

# ----------------------------------------------------------------------------
# This controls whether invalid references are logged.
# ----------------------------------------------------------------------------

runtime.log.invalid.references = true

# ----------------------------------------------------------------------------
# T E M P L A T E  E N C O D I N G
# ----------------------------------------------------------------------------

input.encoding=ISO-8859-1
output.encoding=ISO-8859-1

# ----------------------------------------------------------------------------
# F O R E A C H  P R O P E R T I E S
# ----------------------------------------------------------------------------
# These properties control how the counter is accessed in the #foreach
# directive. By default the reference $velocityCount and $velocityHasNext
# will be available in the body of the #foreach directive.
# The default starting value for $velocityCount is 1.
# ----------------------------------------------------------------------------

directive.foreach.counter.name = velocityCount
directive.foreach.counter.initial.value = 1
directive.foreach.maxloops = -1

directive.foreach.iterator.name = velocityHasNext

# ----------------------------------------------------------------------------
# S E T  P R O P E R T I E S
# ----------------------------------------------------------------------------
# These properties control the behavior of #set.
# For compatibility, the default behavior is to disallow setting a reference
# to null.  This default may be changed in a future version.
# ----------------------------------------------------------------------------

directive.set.null.allowed = false

# ----------------------------------------------------------------------------
# I F  P R O P E R T I E S
# ----------------------------------------------------------------------------
# These properties control the behavior of #if
# Default behavior is to check return value of toString() and treat an object
# with toString() that returns null as null. If all objects have toString()
# methods that never return null, this check is unnecessary and can be disabled
# to gain performance. In Velocity 1.5, no such null check was performed.
directive.if.tostring.nullcheck = true

# ----------------------------------------------------------------------------
# I N C L U D E  P R O P E R T I E S
# ----------------------------------------------------------------------------
# These are the properties that governed the way #include'd content
# is governed.
# ----------------------------------------------------------------------------

directive.include.output.errormsg.start = <!-- include error :
directive.include.output.errormsg.end   =  see error log -->

# ----------------------------------------------------------------------------
# P A R S E  P R O P E R T I E S
# ----------------------------------------------------------------------------

directive.parse.max.depth = 10

# ----------------------------------------------------------------------------
# S C O P E  P R O P E R T I E S
# ----------------------------------------------------------------------------
# These are the properties that govern whether or not a Scope object
# is automatically provided for each of the given scopes to serve as a
# scope-safe reference namespace and "label" for #break calls. The default
# for most of these is false.  Note that <bodymacroname> should be replaced by
# name of macros that take bodies for which you want to suppress the scope.
# ----------------------------------------------------------------------------
# template.provide.scope.control = false
# evaluate.provide.scope.control = false
foreach.provide.scope.control = true
# macro.provide.scope.control = false
# define.provide.scope.control = false
# <bodymacroname>.provide.scope.control = false

# ----------------------------------------------------------------------------
# T E M P L A T E  L O A D E R S
# ----------------------------------------------------------------------------
#
#
# ----------------------------------------------------------------------------

resource.loader = file

file.resource.loader.description = Velocity File Resource Loader
file.resource.loader.class = org.apache.velocity.runtime.resource.loader.FileResourceLoader
file.resource.loader.path = .
file.resource.loader.cache = false
file.resource.loader.modificationCheckInterval = 2

string.resource.loader.description = Velocity String Resource Loader
string.resource.loader.class = org.apache.velocity.runtime.resource.loader.StringResourceLoader

# ----------------------------------------------------------------------------
# VELOCIMACRO PROPERTIES
# ----------------------------------------------------------------------------
# global : name of default global library.  It is expected to be in the regular
# template path.  You may remove it (either the file or this property) if
# you wish with no harm.
# ----------------------------------------------------------------------------
# velocimacro.library = VM_global_library.vm

velocimacro.permissions.allow.inline = true
velocimacro.permissions.allow.inline.to.replace.global = false
velocimacro.permissions.allow.inline.local.scope = false

velocimacro.context.localscope = false
velocimacro.max.depth = 20

# ----------------------------------------------------------------------------
# VELOCIMACRO STRICT MODE
# ----------------------------------------------------------------------------
# if true, will throw an exception for incorrect number 
# of arguments.  false by default (for backwards compatibility)
# but this option will eventually be removed and will always
# act as if true
# ----------------------------------------------------------------------------
velocimacro.arguments.strict = false

# ----------------------------------------------------------------------------
# VELOCIMACRO BODY REFERENCE 
# ----------------------------------------------------------------------------
# Defines name of the reference that can be used to render the AST block passed to
# block macro call as an argument inside a macro.
# ----------------------------------------------------------------------------
velocimacro.body.reference=bodyContent

# ----------------------------------------------------------------------------
# STRICT REFERENCE MODE
# ----------------------------------------------------------------------------
# if true, will throw a MethodInvocationException for references
# that are not defined in the context, or have not been defined
# with a #set directive. This setting will also throw an exception
# if an attempt is made to call a non-existing property on an object
# or if the object is null.  When this property is true then property
# 'directive.set.null.allowed' is also set to true.
# ----------------------------------------------------------------------------
runtime.references.strict = false

# ----------------------------------------------------------------------------
# INTERPOLATION
# ----------------------------------------------------------------------------
# turn off and on interpolation of references and directives in string
# literals.  ON by default :)
# ----------------------------------------------------------------------------
runtime.interpolate.string.literals = true


# ----------------------------------------------------------------------------
# RESOURCE MANAGEMENT
# ----------------------------------------------------------------------------
# Allows alternative ResourceManager and ResourceCache implementations
# to be plugged in.
# ----------------------------------------------------------------------------
resource.manager.class = org.apache.velocity.runtime.resource.ResourceManagerImpl
resource.manager.cache.class = org.apache.velocity.runtime.resource.ResourceCacheImpl

# ----------------------------------------------------------------------------
# PARSER POOL
# ----------------------------------------------------------------------------
# Selects a custom factory class for the parser pool.  Must implement
# ParserPool.  parser.pool.size is used by the default implementation
# ParserPoolImpl
# ----------------------------------------------------------------------------

parser.pool.class = org.apache.velocity.runtime.ParserPoolImpl
parser.pool.size = 20


# ----------------------------------------------------------------------------
# EVENT HANDLER
# ----------------------------------------------------------------------------
# Allows alternative event handlers to be plugged in.  Note that each
# class property is actually a comma-separated list of classes (which will
# be called in order).
# ----------------------------------------------------------------------------
# eventhandler.referenceinsertion.class =
# eventhandler.nullset.class =
# eventhandler.methodexception.class =
# eventhandler.include.class =


# ----------------------------------------------------------------------------
# EVALUATE
# ----------------------------------------------------------------------------
# Evaluate VTL dynamically in template.  Select a class for the Context, if
# you want all #set calls within it to be locally scoped.  This feature is
# deprecated; please use $evaluate to hold local references instead.
# ----------------------------------------------------------------------------

#directive.evaluate.context.class = org.apache.velocity.VelocityContext


# ----------------------------------------------------------------------------
# PLUGGABLE INTROSPECTOR
# ----------------------------------------------------------------------------
# Allows alternative introspection and all that can of worms brings.
# ----------------------------------------------------------------------------

runtime.introspector.uberspect = org.apache.velocity.util.introspection.UberspectImpl


# ----------------------------------------------------------------------------
# SECURE INTROSPECTOR
# ----------------------------------------------------------------------------
# If selected, prohibits methods in certain classes and packages from being 
# accessed.
# ----------------------------------------------------------------------------

introspector.restrict.packages = java.lang.reflect

# The two most dangerous classes

introspector.restrict.classes = java.lang.Class
introspector.restrict.classes = java.lang.ClassLoader
                
# Restrict these for extra safety

introspector.restrict.classes = java.lang.Compiler
introspector.restrict.classes = java.lang.InheritableThreadLocal
introspector.restrict.classes = java.lang.Package
introspector.restrict.classes = java.lang.Process
introspector.restrict.classes = java.lang.Runtime
introspector.restrict.classes = java.lang.RuntimePermission
introspector.restrict.classes = java.lang.SecurityManager
introspector.restrict.classes = java.lang.System
introspector.restrict.classes = java.lang.Thread
introspector.restrict.classes = java.lang.ThreadGroup
introspector.restrict.classes = java.lang.ThreadLocal




