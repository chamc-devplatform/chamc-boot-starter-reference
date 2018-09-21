# 权限控制
    
## 说明

开启security之后，可使用注解进行权限控制，目前支持前后端不分离的模式，前端模板使用thymeleaf。

权限控制通过enable开关控制开启或者关闭，开启的话需要添加redis配置项

需要配置“不做验证的url”和“不做登录过滤的url”

在登录的时候，构造了`LoginUser`对象，在构造该对象的时候，处理了用户的权限、主/兼职机构等，然后登录成功后将信息保存在了session中，后续操作的时候会根据保存的权限信息做验证。

用户的权限既可以通过角色控制，也可以通过具体的权限配置。通过具体的权限配置时，先要在permission表中录入相关的权限信息，然后配置角色-权限关系。

## 配置

    chamc.security.enable=true
    spring.redis.host=localhost
    spring.session.store-type=redis

    #不需要做验证的url，比如静态资源，登录页面等，默认"/js/**"，"/css/**"，"/image/**"，"/webjars/**"
    chamc.security.addtional-none-filter-urls=
    #不需要做登录过滤的url，默认包含"/login", "/index"，"/ajaxLogin"
    chamc.security.addtional-none-login-urls=
    #增加不需要做验证的url
    chamc.security.none-filter-urls=
    #增加不需要做登录过滤的url
    chamc.security.none-login-urls=
    chamc.security.permission.enable=
    #数据源名称，即用户组织、角色权限表在哪个库里，多数据源情况下配置，不配的话取默认数据源
    chamc.security.permission.datesource=

## 后端权限控制

### 注解

在后端的权限控制中，有三种安全注解可供使用：`@Secured`注解、`@RolesAllowed`注解以及表达式驱动的注解（`@PreAuthorize`、`@PostAuthorize`、`@PreFilter`和`@PostFilter`）。推荐使用表达式驱动的注解。

    @PreAuthorize   //在方法调用之前，基于表达式的计算结果来限制对方法的访问
    @PostAuthorize  //允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常
    @PostFilter     //允许方法调用，但必须按照表达式来过滤方法的结果
    @PreFilter      //允许方法调用，但必须在进入方法之前过滤输入值

### 使用

#### `@Secured`和`@RolesAllowed`

`@Secured`和`@RolesAllowed`类似，使用一个String数组作为参数，每个String值是一个权限，调用这个方法至少需要具备其中的一个权限。例如以下两个例子，用户要访问add方法必须有`ADD`角色或者`ADMIN`角色之一；用户要访问delete方法必须有`DELETE`角色或者`ADMIN`角色之一。

    @Secured({"ROLE_ADD","ROLE_ADMIN"})
    public String add(){
       //……
       return "add";
    }
    
    @RolesAllowed({"ROLE_DELETE","ROLE_ADMIN"})
    public String delete(){
       //……
       return "delete";
    }

这两个注解在拒绝未认证用户方面还不错，但是都存在一个缺点：它们只能根据用户有没有授予特定的权限来限制方法的调用，在判断方式是否执行方面，无法使用其他的因素。为此，Spring Security 3.0引入了表达式驱动的注解。

#### 表达式驱动的注解

表达式驱动的注解：这些注解的值参数中都可以接受一个SpEL表达式（[了解更多](https://docs.spring.io/spring-security/site/docs/5.0.0.RELEASE/reference/htmlsingle/#el-access)）。表达式可以是任意合法的SpEL表达式，如果表达式的计算结果为true，那么安全规则通过，否则就会失败。安全规则通过或失败的结果会因为所使用注解的差异而有所不同。

【常用的几个EL表达式】

    hasAnyRole([role1,role2])  //如果用户被授予了列表中任意的指定角色，结果为true
    hasRole([role])            //如果用户被授予了指定的角色，结果为true
    hasAuthority([authority])  //如果用户有指定的权限，结果为true
    hasAnyAuthority([authority1,authority2])  //如果用户有列表中任意的权限，结果为true

示例：

    @PreAuthorize("hasRole('ADMIN')")
    public String list(){……}
    
    @PreAuthorize("hasAnyAuthority('project:view','project:*')")
    public String list(){……}

【四个注解】

    @PreAuthorize   //在方法调用之前，基于表达式的计算结果来限制对方法的访问
    @PostAuthorize  //允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常
    @PostFilter     //允许方法调用，但必须按照表达式来过滤方法的结果
    @PreFilter      //允许方法调用，但必须在进入方法之前过滤输入值

**@PreAuthorize** 表达式会在方法调用之前执行，如果表达式的计算结果不为true的话，将会阻止方法执行。与`@Secured`和`@RolesAllowed`相比，除了可以限制角色以外，还可以进行参数的验证。例如：

    //用户有\`project:view\`权限，并且入参size&gt;0时，才能访问该方法
    @PreAuthorize("hasAuthority('project:view') and #size>0")
    @GetMapping("view")
    public String list(int size) {
       return "project";
    }

**@PostAuthorize** 表达式直到方法返回才会执行，然后决定是否抛出安全性的异常。除了验证的时机之外，`@PostAuthorize`与`@PreAuthorize`的工作方式差不多，只不过它会在方法执行之后，才会应用安全规则。例如：

    //returnObject表示返回的对象，本例中若返回列表中对象的个数与size不等，将抛出异常
    @PostAuthorize("returnObject.size() == #size")
    @GetMapping("postAuth")
    public List testPost(int size) {
        …… 
        return books;
    }

**@PostFilter** 会使用这个表达式计算该方法所返回集合的每个成员，将计算结果为false的成员移除掉。

**@PreFilter** 过滤的是要进入方法中的集合成员，只有满足SpEL表达式的元素才会留在集合中。 

## 前端权限控制

在标签中加`sec:authorize`属性，其值也是SpEL表达式，例如：

```
<li sec:authorize="hasAuthority('project:view')">查看项目</li>
```

只有有project:view权限的用户才能看到该菜单。