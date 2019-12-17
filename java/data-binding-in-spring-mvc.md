## 如何转换前端传过来的Date类型数据？—— Spring MVC 中的数据绑定

HTTP 请求中传递的数据都是字符串类型的，当请求到达后端，这些数据需要被转换成Controller方法（即handler）中形参对应的数据类型，如String、Integer、Date等。

常见的数据类型，Spring MVC 都可以将字符串（如1024）正确地转换成对象（如new Integer(1024)），但是对于日期类型`java.util.Date`，由于其对应的字符串形式多种多样，因此若没有进行相关的配置，系统将无法将字符串解析成Date对象。

为了将字符串数据解析成Java中的类实例，Spring MVC 要求我们为类配置实现`java.beans.PropertyEditor`接口的bean，系统会根据解析的目标类来选择相应的`java.beans.PropertyEditor` bean，调用该bean的方法完成解析，生成相应的类实例。

不同的类有不同的`PropertyEditor`，Spring 默认提供一些常用的`PropertyEditor`，它们位于`spring-beans`的`org.springframework.beans.propertyeditors`包路径下，如`CustomDateEditor`就是用来将字符串解析成Date实例的。如果有需要，也可以定制自己的`PropertyEditor`。

在 Spring MVC 中，有关数据绑定`org.springframework.web.bind.WebDataBinder`

### WebDataBinder

在 Spring MVC 中，

