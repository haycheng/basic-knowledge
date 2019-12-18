## 如何转换前端传过来的Date类型数据？—— Spring MVC 中的数据绑定（data binding）

### 如何解析 HTTP 请求中的字符串
HTTP 请求中传递的数据都是字符串类型的，当请求到达后端，这些数据需要被转换成Controller的方法（即handler）中形参对应的数据类型，如String、Integer、Date等。

如果不借助系统提供的功能，我们可以自己对字符串数据进行处理。比如在某个handler中，需要请求方传一个`Date`（`java.util.Date`）类型的数据。如果系统不能自动将字符串转换成`Date`，那我们可以将这个入参定义为`String`（`java.lang.String`）类型，然后在方法体中对这个字符串进行解析（如用`java.text.DateFormat#format(java.util.Date)`），后面再用这个`Date`实例进行业务处理。

### Spring MVC 是如何将HTTP中的字符串转换成Java类实例的？
常见的数据类型，Spring MVC 都可以将其（如字符串"1024"）正确地转换成Java对象（如 Integer(1024)），但是对于日期类型`Date`，由于其对应的字符串形式多种多样，因此若没有进行相关的配置，系统将无法将字符串解析成Date对象。

为了将字符串数据解析成Java中的类实例，Spring MVC 要求我们为类配置实现`PropertyEditor`（`java.beans.PropertyEditor`，属性编辑器）接口的bean，系统会根据解析的目标类来选择相应的`PropertyEditor` bean，调用该bean的方法完成解析，生成相应的类实例。

不同的类有不同的`PropertyEditor`，Spring 默认提供一些常用的`PropertyEditor`，它们位于`spring-beans`的`org.springframework.beans.propertyeditors`包路径下。例如，`CustomDateEditor`是用来将字符串解析成`Date`实例的，该类的构造方法中，要求传入一个`DateFormat`实例以指定待解析的字符串的日期格式，一个是否允许待解析字符串为空的boolean参数，还可以额外指定日期字符串的长度。如果有需要，也可以定制自己的`PropertyEditor`。

`PropertyEditor`除了用于Java类的实例化外，还可以通过它进行一些其他的数据预处理操作。比如，`org.springframework.beans.propertyeditors.StringTrimmerEditor`可以去除字符串两边的空格等空白字符，然后将其转换成Java中的String实例。

### 如何注册`PropertyEditor` Bean？
在 Spring MVC 中，添加的`PropertyEditor` bean 是注册到类`WebDataBinder`（`org.springframework.web.bind.WebDataBinder`）实例中的。注册`PropertyEditor` bean 的方法是`registerCustomEditor`，该方法有两个重载形式，其中参数最全的方法签名为`void registerCustomEditor(@Nullable Class<?> requiredType, @Nullable String propertyPath, PropertyEditor propertyEditor)`。这些参数的含义如下：

> requiredType：指定当前注册的`PropertyEditor` bean 用于何种Java类的实例化。如要将字符串解析成Date实例，则此处填写`Date.class`。

> propertyPath：进一步限定当前的`PropertyEditor` bean 用于上述Java类哪个属性的实例化。如果该值为`null`，则将作用于该Java类所有属性。

> propertyEditor：即注册的`PropertyEditor` bean。

在Spring MVC中，一般是通过给某个方法加上`@InitBinder`（`org.springframework.web.bind.annotation.InitBinder`），并在此方法体实现注册`PropertyEditor` bean 的逻辑。`@InitBinder`用于在@Controller中标注方法，表示该方法将为当前Controller注册`PropertyEditor`，注册的`PropertyEditor`只对当前的Controller有效。该方法的入参中通常有一个`WebDataBinder`实例，在方法中调用该实例的`registerCustomEditor`方法进行注册即可。比如：
```
@InitBinder
public void initBinder(WebDataBinder binder){
    DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    CustomDateEditor dateEditor = new CustomDateEditor(df, true);
    binder.registerCustomEditor(Date.class,dateEditor);
}
```
@InitBinder有个参数，

关于`PropertyEditor` bean 的作用域？

### `PropertyEditor` Bean 是如何发挥作用的？
添加了`PropertyEditor` bean

### @DateTimeFormat 注解

### 参考文档
1. [SpringMVC注解@initbinder解决类型转换问题](https://www.cnblogs.com/aheizi/p/5440884.html)
1. [What is the purpose of init binder in spring MVC](https://stackoverflow.com/questions/5211323/what-is-the-purpose-of-init-binder-in-spring-mvc)
1. [How to Use @Initbinder in Spring MVC](https://medium.com/stackavenue/how-to-use-initbinder-in-spring-mvc-ecb974a6884)

