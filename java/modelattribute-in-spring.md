# Spring中`@ModelAttribute`注解的使用

## 注解的功能
jar包spring-web下`@ModelAttribute`（`org.springframework.web.bind.annotation.ModelAttribute`）注解，用于向web视图即`Model`中添加属性，从而可以在视图中通过${}使用这些属性数据。该注解只能用在带`@Controller`或`@ControllerAdvice`注解的类中，它可以标注在方法参数或方法上。可用`@ModelAttribute`的参数包括方法的入口参数和返回参数，可用`@ModelAttribute`的方法包括带`@RequestMapping`注解的方法和不带`@RequestMapping`的普通方法。

## 在方法入口参数上使用`@ModelAttribute`
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

在上述代码中，首先系统会自动将HTTP请求中的数据，按数据的key与`User`类中的字段名之间的对应关系，将数据的value赋给作为入参的`User`实例。然后，由于存在注解`@ModelAttribute`，系统会向`Model`中添加一个属性，属性的key为`someUser`（如果不指定该值，则key默认为类名首字母小写，即`user`），value为入参的User实例。

此时如果方法体没有标注`@SessionAttributes("user")`，那么scope为request，如果标注了，那么scope为session。

## 在方法返回参数上使用`@ModelAttribute`


## 在@RequestMapping方法上使用`@ModelAttribute`

## 在普通方法上使用`@ModelAttribute`
被`@ModelAttribute`注解的方法，会在当前Controller的每个方法带`@RequestMapping`的方法每次执行前被执行。

根据被注解方法的的返回值，可分为两种情况：方法有返回值的，方法无返回值的。

### 方法有返回值的情况
```
@Controller
public class HelloWorldController {

    @ModelAttribute 
    public Account addAccount(@RequestParam String number) { 
        return accountManager.findAccount(number); 
    } 

    @RequestMapping(value = "/helloWorld")
    public String helloWorld(@ModelAttribute("someUser") User user) {
        user.setUserName("awesome");
        return "helloWorld";
    }
}
```

### 方法无返回值的情况
```
@Controller
public class HelloWorldController {

    @ModelAttribute 
    public void addAccount(@RequestParam String number) { 
        return accountManager.findAccount(number); 
    } 

    @RequestMapping(value = "/helloWorld")
    public String helloWorld(@ModelAttribute("someUser") User user) {
        user.setUserName("awesome");
        return "helloWorld";
    }
}
```

这个例子，在获得请求/helloWorld 后，populateModel方法在helloWorld方法之前先被调用，它把请求参数（/helloWorld?abc=text）加入到一个名为attributeName的model属性中，在它执行后 helloWorld被调用，返回视图名helloWorld和model已由@ModelAttribute方法生产好了。
　　这个例子中model属性名称和model属性对象有model.addAttribute()实现，不过前提是要在方法中加入一个Model类型的参数。
　　当URL或者post中不包含次参数时，会报错，其实不需要这个方法，完全可以把请求的方法写成，这样缺少此参数也不会出错

### 参考文档
1. [博客园 - spring学习之@ModelAttribute运用详解](https://www.cnblogs.com/javaboy2018/p/8953415.html)
1. [CSDN - 【Spring】@ModelAttribute三种使用场景](https://blog.csdn.net/wxgxgp/article/details/81304570)

