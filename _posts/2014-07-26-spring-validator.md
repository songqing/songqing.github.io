---
layout: post
category : spring
date: 2014/07/26 21:21:52 
title: Spring验证详解
tags : spring
---


目标：
Spring自带的验证方式
基于JSR303的验证
国际化显示错误信息
一：使用Spring自带的验证方式
       该种方式相对来说麻烦一点，需要为每个需要验证的实体类编写一个验证类，不过好处是可以处理多字段组合验证以及业务逻辑等复杂验证

1.1：定义实体类（User）

{% highlight java %}
public class User {

    private String username;

    private String nickname;

    private String password;

   //省略getter /setter
}

{% endhighlight %}



1.2:定义实体类验证类（UserValidator）
需要继承Validator接口

{% highlight java %}

public class UserValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return User.class.equals(clazz); 
    }

public void validate(Object target, Errors errors) {
       User user = (User)target;
        if(StringUtils.isEmpty(user.getUsername())){
            //1:使用这种方式，会报错(No message found under code 'empty.user.username.user.username' for locale 'zh_CN'.)，因为rejectValue的第二个参数都是errorcode
            //errors.rejectValue("username","empty.user.username");
            //errors.rejectValue("username","用户名不能为空");

            //2:正确用法：rejectValue(String filed,String errorcode,Object[] errorArgs,String defaultMessage)
            errors.rejectValue("username","empty.user.username","用户名不能为空Default");

            //使用该种方式，不需要配置ResourceBundleMessageSource
           // ValidationUtils.rejectIfEmpty(errors,"username","empty.user.username","用户名不能为空");
        } else {
           int length =  user.getUsername().length();
            if(length<=3){
                errors.rejectValue("username","min.length.user.username","用户名长度不能小于3Default");
            }else if (length>=6){
                errors.rejectValue("username","max.length.user.username","用户名不能大于6Default");
            }

        }
        }

}
{% endhighlight %}


1.3：编写控制器代码：


{% highlight java %}

 @RequestMapping(value = "validatorBySpring", method = RequestMethod.GET)
    public String validatorBySpringForm(@ModelAttribute("user") User user) {
        return "validator/validatorBySpring";
    }

    /**
     * 第一种，使用spring自带的validator
     */

    @RequestMapping(value = "validatorBySpring", method = RequestMethod.POST)
    public String validatorBySpring(@ModelAttribute("user") User user,
                                    BindingResult result) {
        userValidator.validate(user, result);
        if (result.hasErrors()) {
            return "validator/validatorBySpring";
        }
        return "validator/success";
    }

{% endhighlight %}

注意：在get方法中，需要加上@ModelAttribute，否则无法访问，或者可以抽出来，写一个以下方法：


{% highlight java %}

@ModelAttribute(“user")
public　void　getUser(){
     return new User();
}

{% endhighlight %}
这样的话，访问该控制器的时候都会调用该方法


1.4：前台
          

{% highlight html %}

           <form:form action="${ctx}/validator/validatorBySpring" method="post" modelAttribute="user">
               <tr>
                   <td><form:label path="username" >Username</form:label></td>
                   <td><form:input path="username" ></form:input></td>
                   <td><form:errors path="username"></form:errors></td>
               </tr>

               <tr>
                   <td> <form:label path="password">password</form:label>  </td>
                   <td> <form:input path="password"></form:input>     </td>
                   <td><form:errors path="password"></form:errors> </td>
               </tr>

               <tr>
                   <td> <form:label path="email">email</form:label>  </td>
                   <td> <form:input path="email"></form:input>     </td>
                   <td><form:errors path="email"></form:errors> </td>
               </tr>
               <tr>
                   <td colspan="3">
                       <input type="submit" value="SUBMIT">
                   </td>
               </tr>

           </form:form>
 
{% endhighlight %}
      

1.5: 在上述代码已经可以完成Spring自带验证了，而且界面能正常显示错误信息，但是在实际过程中，错误信息采用的是硬编码，这是不可取的，所以，接下来需要国际化错误信息
    

{% highlight xml %}

    <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basename">
            <value>messages</value>
        </property>
    </bean>

{% endhighlight %}

messages_zh_CN.properties 在resources/下
empty.user.username=用户名不能为空
min.length.user.username=用户名长度不能小于3
max.length.user.username=用户名长度不能大于6

其中的errorcode可以随便写，只要在messages中对应上即可，在这种情况下，在实体验证类中，就可以使用第一种方式即：errors.rejectValue("username",”empty.user.username)。因为在message文件中有对应的errorcode，所以能正常显示



扩展：
1：不使用rejectValue，而是使用reject，界面该如何显示
eg:errors.reject(“empty.user.username”,”用户名不能为空");
直接在form中写如下：<code><form:errors /></code>会显示全部错误

2:使用ValidationUtils
ValidationUtils.rejectIfEmpty(errors,"username","empty.user.username","用户名不能为空”);





 Errors主要使用方法：

{% highlight java %}

reject(String errorcode,Object[] errorArgs,String defaultMessage)

rejectValue(String filed,String errorcode,Object[] errorArgs,String defaultMessage)

BinderResult接口扩展了Erros接口，以便可以使用Spring的Validator对对象进行校验，同时获取绑定结果对象的信息。

{% endhighlight %}

二：使用基于jsr303的验证
在Spring3.x企业开发实战里面提到：通过binder.setValidator之后，Spring MVC将使用它对入参对象进行校验，将不再使用Spring框架装配的Validator对入参进行校验。换句话说：即使在入参上标注了@Valid注解，也不会再根据入参对象类中的jsr303注解进行校验了。

2.1：
1:使用jsr303注解，可以注解基于javax.validator或者基于hibernated 。之前觉得没有什么不同，但是今天发现还是有不同的

2.1.1使用之前的配置，不做任何修改,messages里面没有任何对应的errorcode


{% highlight java %}
@NotEmpty
{% endhighlight %}

起作用了，提示：may not be empty ，为什么会显示这个会有解释


{% highlight java %}

@NotEmpty(message = "{username.not.empty}")
{% endhighlight %}
提示：{username.not.empty}

不起作用的原因是：NotNull和NotEmpty是不同滴。

@NotNull
并没有进行验证，而是直接通过，也就是验证没有起作用

@NotNull(message = "{username.not.empty}”)
仍然没有起作用

2.1.2：在资源文件中配置如下：
username.not.empty=用户名不能为空1
@NotEmpty
显示  may not be empty

 @NotEmpty(message = "{username.not.empty}")
提示：{username.not.empty} 。即，直接显示message里面的内容，而我要的是显示：用户名不能为空1

2.1.3：在资源文件配置如下：
username.not.empty=用户名不能为空1
NotNull.user.username=用户名不能为空
NotEmpty.user.username=用户名不能为空


    @NotEmpty 提示：用户名不能为空

    @NotEmpty(message = "{username.not.empty}”)   提示用户名不能为空，即：message里面的没有起作用，

去掉NotEmpty.user.username=用户名不能为空，则提示为{username.not.empty}



2.1.4：加上以上配置之后

{% highlight xml %}

<!-- 以下 validator  ConversionService 在使用 mvc:annotation-driven 会 自动注册-->
    <bean id="validator"
          class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
        <property name="providerClass"  value="org.hibernate.validator.HibernateValidator"/>
        <!-- 如果不加默认到 使用classpath下的 ValidationMessages.properties -->
        <property name="validationMessageSource" ref="messageSource"/>
    </bean>


{% endhighlight %}


资源文件还是如下：
username.not.empty=用户名不能为空1
NotNull.user.username=用户名不能为空
NotEmpty.user.username=用户名不能为空

注意，上面的并没有在mvc:annoation-driver中注册


 @NotEmpty 提示用户名不能为空

@NotEmpty(message = "{username.not.empty}")提示用户名不能为空，即messages仍然没有起作用

去掉NotEmpty.user.username=用户名不能为空，则提示为{username.not.empty}



将<code><mvc:annotation-driven validator="validator" /></code>

@NotEmpty 提示用户名不能为空

@NotEmpty(message = "{username.not.empty}")提示用户名不能为空，即messages仍然没有起作用

去掉NotEmpty.user.username=用户名不能为空，则提示为：用户名不能为空1

总结：因为不知道为什么基于javax.validator的注解不起作用，所以字总结基于hibernate的注解。这里一NotEmpty来举例。
首先，如果一旦在实体上标识了该注解，则就已经起作用了。如果不配置错误信息，将使用默认的：在hibernate-validator.jar中的Resource中，有一个ValidatorMessages.properties。里面有错误主力的默认显示：org.hibernate.validator.constraints.NotEmpty.message=may not be empty，这也就是为什么只配置了@NotEmpty之后显示 may not be empty的原因。当配置了自己的资源文件后，因为没有配置validator，所以还是显示之前的错误。那为什么后面没有配置validator。为什么NotEmpty.user.usrname能显示而{username.not.empty}不能正常显示呢？这是因为FileError实现了MessageResourceResolvable接口，里面有自己的一套规则：
Annotation.entity.attribute。所以，我使用NotEmpty.user.username能显示中文，而使用{usrname.not.empty}却显示{username.not.empty}。在配置了validator之后，因为
<code><mvc:annotaitonDriver/></code> 中没有指定validator，所以相当于没有配置。即：2.1.3的测试结果和2.1.4的测试结果一样。只有配置了 mvc:annotaiton-driver validator=“validator”之后，才能自定义错误格式，这种情况下才@NotEmpty{message={username.not.empty}}才能显示出来，不过NotEmpty.user.username这样格式的优先级比较高，两者放在一起，还是会显示后者。这就是，为什么在所有的一切都配置好之后，还是会显示“用户名不能为空”，只有在messages里面去掉之后才能显示username.not.empty的信息。






问题：
1：基于spring自带的validator前台不使用Spring form标签如何展示错误？ 
2：使用ajax方式，怎样进行验证，和上面是同样的问题，也就是不使用表单

其中，基于jsr303的可以实现，可以参考springside里面的代码，但是自带的却不好实现，因为循环FiledError的时候，只能得到errorcode和defultMessage，得不到国际化信息


资料：
JSR 303 - Bean Validation 介绍及最佳实践
http://www.ibm.com/developerworks/cn/java/j-lo-jsr303/

开涛的博客：跟我学Spring mvc 3
http://jinnianshilongnian.iteye.com/blog/1617451

spring 3.x 企业开发实战








</pre>