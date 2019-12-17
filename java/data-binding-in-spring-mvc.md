## 如何转换前端传过来的Date类型数据？—— Spring MVC 中的数据绑定

HTTP 请求中传递的数据都是字符串类型的，当请求到达后端，这些数据需要被转换成Controller方法（即handler）中形参对应的数据类型，如String、Integer、Date等。

常见的数据类型，Spring MVC 都可以将字符串（如1024）正确地转换成对象（如new Integer(1024)），但是对于日期类型`java.util.Date`，由于其对应的字符串形式多种多样，因此若没有进行相关的配置，系统将无法将字符串解析成Date对象。

### Spring MVC 是如何将字符串转换成Java类实例的？
为了将字符串数据解析成Java中的类实例，Spring MVC 要求我们为类配置实现`PropertyEditor`（`java.beans.PropertyEditor`）接口的bean，系统会根据解析的目标类来选择相应的`PropertyEditor` bean，调用该bean的方法完成解析，生成相应的类实例。

不同的类有不同的`PropertyEditor`，Spring 默认提供一些常用的`PropertyEditor`，它们位于`spring-beans`的`org.springframework.beans.propertyeditors`包路径下，如`CustomDateEditor`就是用来将字符串解析成Date实例的。如果有需要，也可以定制自己的`PropertyEditor`。

`PropertyEditor`除了用于Java类的实例化外，还可以通过它进行一些数据的预处理。比如，`org.springframework.beans.propertyeditors.StringTrimmerEditor`可以去除字符串两边的空格等空白字符，然后将其转换成Java中的String实例。

### 如何注册`PropertyEditor` Bean？
在 Spring MVC 中，添加的`PropertyEditor` bean 是注册到类`WebDataBinder`（`org.springframework.web.bind.WebDataBinder`）实例中的。注册`PropertyEditor` bean 的方法是`registerCustomEditor`，该方法有两个重载形式，其中参数最全的方法签名为`void registerCustomEditor(@Nullable Class<?> requiredType, @Nullable String propertyPath, PropertyEditor propertyEditor)`。这些参数的含义如下：

> requiredType：指定当前注册的`PropertyEditor` bean 用于何种Java类的实例化。如要将字符串解析成Date实例，则此处填写`Date.class`。

> propertyPath：进一步限定当前的`PropertyEditor` bean 用于上述Java类哪个属性的实例化。如果该值为`null`，则将作用于该Java类所有属性。

> propertyEditor：即注册的`PropertyEditor` bean。

在Spring MVC中，一般是给某个方法加上`@InitBinder`（`org.springframework.web.bind.annotation.InitBinder`）来注册`PropertyEditor` bean 的。该方法的入参中通常有一个`WebDataBinder`实例，在方法中调用该实例的`registerCustomEditor`方法进行注册即可。比如：
```
@InitBinder
public void initBinder(WebDataBinder binder){
    DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    CustomDateEditor dateEditor = new CustomDateEditor(df, true);
    binder.registerCustomEditor(Date.class,dateEditor);
}
```

关于`PropertyEditor` bean 的作用域？

### `PropertyEditor` Bean 是如何发挥作用的？
添加了`PropertyEditor` bean



