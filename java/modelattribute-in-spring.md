# Spring中`@ModelAttribute`注解的使用

## 注解的功能
jar包spring-web下的`@ModelAttribute`（`org.springframework.web.bind.annotation.ModelAttribute`）注解，从其名字可以看出，它用于向web视图即model中添加属性，从而可以在视图中通过${}使用这些属性数据。该注解只能用在带`@Controller`或`@ControllerAdvice`注解的类中，它可以标注在**方法参数**或**方法**上。

可用`@ModelAttribute`的参数包括方法的入口参数和返回参数，可用`@ModelAttribute`的方法包括带`@RequestMapping`注解的方法和不带`@RequestMapping`的普通方法。

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

在上述代码中，首先系统会自动将HTTP请求中的数据，按数据的key与`User`类中的字段名之间的对应关系，将数据的value赋给作为入参的`User`实例。然后，由于存在注解`@ModelAttribute`，系统会向model中添加一个属性，属性的key为`someUser`（如果不指定该值，则key默认为类名首字母小写，即`user`），value为入参的User实例。

此时如果方法体没有标注`@SessionAttributes("user")`，那么scope为request，如果标注了，那么scope为session。

## 在方法返回参数上使用`@ModelAttribute`


## 在@RequestMapping方法上使用`@ModelAttribute`

```
@Controller 
public class HelloWorldController {

    @Autowired
    private AccountManager accountManager;

    @RequestMapping(value = "/helloWorld") 
    @ModelAttribute("accountName") 
    public String helloWorld(@RequestParam String uid) {
        Account account = accountManager.findAccountById(uid);
        return account.getName(); 
    } 
  }
```
在例子中，`helloWorld()`方法的返回值不是视图名称，而是向model添加的属性值，属性名称则为"accountName"，即该方法会向model中添加名为"accountName"、值为获取的账户名称的属性。

视图名称由`RequestToViewNameTranslator`（`org.springframework.web.servlet.RequestToViewNameTranslator`）根据请求路径"/helloWorld"转换为逻辑视图名称"helloWorld"。

## 在普通方法上使用`@ModelAttribute`
被`@ModelAttribute`注解的方法会在当前Controller带`@RequestMapping`的方法每次执行前被执行，以便向model中添加属性。

这些普通方法的入参可以使用`@RequestParam`，以便将HTTP请求的数据赋给该参数。

根据被注解方法的的返回值，可分为两种情况：方法有返回值的，方法无返回值的。

### 方法有返回值的情况
此时方法的返回值会添加到model中。
```
@Controller
public class HelloWorldController {
    
    @Autowired
    private AccountManager accountManager;
    
    @ModelAttribute(name = "user")
    public Account addAccount(@RequestParam String uid) { 
        return accountManager.findAccountById(uid);
    }

    @RequestMapping(value = "/helloWorld")
    public String helloWorld() {
        return "helloWorld";
    }
}
```
在例子中，`addAccount()`方法会向model中添加一个名为"user"、值为返回值的属性。如果`@ModelAttribute`中没有指定属性的名称，则**属性名由返回类型确定**，即将"Account"的首字母小写，得到属性名为"account"。


### 方法无返回值的情况
此时方法的入参中需要有一个`Model`（`org.springframework.ui.Model`）类型的参数，以便向model中添加属性。
```
@Controller
public class HelloWorldController {

    @Autowired
    private AccountManager accountManager;
    
    @ModelAttribute
    public void addAccount(@RequestParam String uid, Model model) { 
        Account account = accountManager.findAccountById(uid);
        model.addAttribute("user", account);
        model.addAttribute("status", 1);
    }

    @RequestMapping(value = "/helloWorld")
    public String helloWorld() {
        return "helloWorld";
    }
}
```
在例子中，`addAccount()`方法向model中添加了两个属性，一个是通过传入的uid获取的Account对象，另一个是状态信息。

### 参考文档
1. [博客园 - spring学习之@ModelAttribute运用详解](https://www.cnblogs.com/javaboy2018/p/8953415.html)
1. [CSDN - 【Spring】@ModelAttribute三种使用场景](https://blog.csdn.net/wxgxgp/article/details/81304570)

