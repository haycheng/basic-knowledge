## Spring中`@ModelAttribute`注解的使用

### 注解的功能
jar包spring-web下`@ModelAttribute`（`org.springframework.web.bind.annotation.ModelAttribute`）注解，用于向web视图即`Model`中添加属性，从而可以在视图中通过${}使用这些属性数据。该注解只能用在带`@Controller`或`@ControllerAdvice`注解的类中，它可以标注在方法参数或方法上。可用`@ModelAttribute`的参数包括方法的入口参数和返回参数，可用`@ModelAttribute`的方法包括带`@RequestMapping`注解的方法和不带`@RequestMapping`的普通方法。

### 在方法入口参数上使用`@ModelAttribute`
```
@Controller
public class HelloWorldController {

    @RequestMapping(value = "/helloWorld")
    public String helloWorld(@ModelAttribute("someUser") User user) {
        user.setUserName("awesome");
        return "helloWorld";
    }
}
```

在上述代码中，系统会自动将HTTP请求中的数据，按数据的key与`User`类中的字段名之间的对应关系，将数据的value赋给作为入参的`User`实例。然后，由于存在注解`@ModelAttribute`，系统会向`Model`中添加一个属性，属性的key为`someUser`（如果不指定该值，则key默认为类名首字母小写，即`user`），value为入参的User实例。

此时如果方法体没有标注`@SessionAttributes("user")`，那么scope为request，如果标注了，那么scope为session。

### 在方法返回参数上使用`@ModelAttribute`


在@RequestMapping方法上使用`@ModelAttribute`

在普通方法上使用`@ModelAttribute`
