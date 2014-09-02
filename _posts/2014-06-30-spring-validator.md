---
layout: post
title:  "Spring验证"
date:   2014/06/30 14:17:14 
category: Spring
tag: spring
---

在spring中，有三种不同的方式执行验证，基于注解，基于spring validator或者混合两种，并没有一种所谓的“最好的”的验证方式，但是也许有一种适合你！

Spring支持Bean Validation1.0(JSR-303)和Bean Validation1.1(JSR-349)，为了保持向前兼容，同时支持Spring validator接口。


{% highlight java %}

	public class User{
	   private String name;
	   .....
	}


{% endhighlight %}

###第一种：###
如果使用spring3.x和简单的验证，使用<code>javax.validation.constraints</code>注解。即jsr-303


{% highlight java %}

	@NotNull
	public class User{
	   private String name;
	   .....
	}

{% endhighlight %}

使用这种方式，需要一个实现了jsr303的库，比如Hibernate Validator。这样你可以在控制器中，这样写：


{% highlight java %}

    public createUser(Model model, @Valid @ModelAttribute("user") User user, BindingResult result){
    if (result.hasErrors()){
      // do something
    }
    else {
      // do something else
    }
}


{% endhighlight %}


**注意**  <code>@Valid</code> 如果user有一个空的name，<code>result.hasError()</code>将返回true。


###第二种

如果是复杂的验证（比如业务逻辑比较复杂，一个验证规则设计多个字段时）或者因为某些方式无法使用第一种验证方式，使用手工的方式是比较好的，这是一个很好的做法，控制器的代码和验证逻辑中分离出来。不要从头开始创建你的验证类（ES），Spring提供了一个方便的接口<code>org.springframework.validation.Validator</code>来实现这种方式


使用Spring Validator接口进行验证主要步骤如下：
  
  - 定义实体类 Person
  - 定义实体验证类并实现Validator接口 PersonValidator

{% highlight java %}


public class User {

    private String name;

    private Integer birthYear;
    private User responsibleUser;
    ...

}


{% endhighlight %}


如果你想做一个比较复杂的验证，比如用户的名字小于18

{% highlight java %}


public class UserValidator implements Validator {

    @Override
    public boolean supports(Class clazz) {
      return User.class.equals(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
      User user = (User) target;

      if(user.getName() == null) {
          errors.rejectValue("name", "your_error_code");
      }

      // do "complex" validation here

    }

}


{% endhighlight %}

在控制器中可以这样写：

{% highlight java %}


@RequestMapping(value="/user", method=RequestMethod.POST)
    public createUser(Model model, @ModelAttribute("user") User user, BindingResult result){
        UserValidator userValidator = new UserValidator();
        userValidator.validate(user, result);

        if (result.hasErrors()){
          // do something
        }
        else {
          // do something else
        }
}


{% endhighlight %}


###验证需要注意的：

  - 前台使用ajax或者表单提交都要验证到。
  - 前端能友好的显示错误。
  - 如果使用单元测试，需要注意将validator对象注入，因为在mvc层面，加载<mvc:annotation-driver>的时候会自动注入validator   


1:验证位置

- 1.1：前端

- 1.2：服务器端

2：表单提交方式

- 2.1：直接使用form表单提交。--这种提交方式没有使用前端验证，需要服务端进行多重验证而且无法将验证的错误漂亮的返回前端。所以不可取，但是却是现在的项目中最常使用的。</li>

- 2.2：使用Spring form表单提交。--这种提交方式和上面一种提交方式类似，但是前端能友好的显示错误信息，但是需要所有的表单都使用Spring form标签。</li>

- 2.3：jQuery form表单提交。，前端jsp页面中不需要使用spring form标签</li>
 
- 2.4：Ajax提交。--这种提交方式，前端jsp页面中不需要使用spring form标签，但是

3：前端验证方式
  
  使用jQuery.validator

4：服务端验证方式

- 4.1：spring自带验证。这种验证方式需要为每个表单编写一个验证类，并实现validator接口。
- 4.2：java validator。这种验证方式需要表单中的name和实体属性相同。
- 4.3：自定义验证方式。

综合：

使用spring form提交和jQuery form 以及ajax提交
服务端最好采用java validator验证，如果之前jsp中定义的name和实体中的不同，则采用自定义的验证方式。

1：一般来说，web项目的验证分为前端和服务器端。前端一般使用jQuery来进行一些非空，字段大小，输入规格等。
但是前端验证后服务器端也需要进行验证。
