### 2.5 参数验证

#### 2.5.1 参数验证的使用

* spring和web组件实现了一些简单的参数验证的方法，使得接口免去了一些重复的冗余的验证代码。常用的validation有：

  ```text
    Bean Validation 中内置的 constraint         
    @Null   被注释的元素必须为 null    
    @NotNull    被注释的元素必须不为 null    
    @AssertTrue     被注释的元素必须为 true    
    @AssertFalse    被注释的元素必须为 false    
    @Min(value)     被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
    @Max(value)     被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
    @DecimalMin(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
    @DecimalMax(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
    @Size(max=, min=)   被注释的元素的大小必须在指定的范围内    
    @Digits (integer, fraction)     被注释的元素必须是一个数字，其值必须在可接受的范围内    
    @Past   被注释的元素必须是一个过去的日期    
    @Future     被注释的元素必须是一个将来的日期    
    @Pattern(regex=,flag=)  被注释的元素必须符合指定的正则表达式    

    Hibernate Validator 附加的 constraint    
    @NotBlank(message =)   验证字符串非null，且长度必须大于0    
    @Email  被注释的元素必须是电子邮箱地址    
    @Length(min=,max=)  被注释的字符串的大小必须在指定的范围内    
    @NotEmpty   被注释的字符串的必须非空    
    @Range(min=,max=,message=)  被注释的元素必须在合适的范围内 
  ```

* 示例：

在入参的类里为需要验证的属性添加validation：

```text
public @Data class GetLoginParam {

    private @NotBlank String username;
    private @Length(min = 6,max = 16)String password;
}
```

在controller方法的参数处添加`@Valid`：

```text
@GetMapping("login")
public ResponseEntity<User> login(@Valid GetLoginParam param){

    return null;
}
```

请求时，参数未通过验证就会报错：

```text
{
    "timestamp": "2017-12-27 15:13",
    "status": 400,
    "error": "Bad Request",
    "exception": "org.springframework.validation.BindException",
    "errors": [
        {
            "codes": [
                "Length.getLoginParam.password",
                "Length.password",
                "Length.java.lang.String",
                "Length"
            ],
            "arguments": [
                {
                    "codes": [
                        "getLoginParam.password",
                        "password"
                    ],
                    "arguments": null,
                    "defaultMessage": "password",
                    "code": "password"
                },
                16,
                6
            ],
            "defaultMessage": "长度需要在6和16之间",
            "objectName": "getLoginParam",
            "field": "password",
            "rejectedValue": "123",
            "bindingFailure": false,
            "code": "Length"
        }
    ],
    "message": "Validation failed for object='getLoginParam'. Error count: 1",
    "path": "/user/login"
}
```

#### 2.5.2 参数验证的扩展

* 示例：写一个验证，验证参数必须不为空且都为大写字母。如下：

1） 先写一个注解：

```text
@Target({ ElementType.FIELD, ElementType.PARAMETER })
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = UppercaseValidator.class)
@Documented
public @interface Uppercase {

    String message() default "必须不为空且全为大写";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };

}
```

2） 再写一个类实现ConstraintValidator：

```text
public class UppercaseValidator implements ConstraintValidator<Uppercase,String>{

    @Override
    public void initialize(Uppercase constraintAnnotation) {
        // TODO Auto-generated method stub

    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        if (value != null && value.length() > 0){
            for(int i = 0; i < value.length(); i++){
                if (Character.isUpperCase(value.charAt(i))) continue;
                else return false;
            }
            return true;
        }
        return false;
    }

}
```

3） 按照上一点的使用方法使用即可。