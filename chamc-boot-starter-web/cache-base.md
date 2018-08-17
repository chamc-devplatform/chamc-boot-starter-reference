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