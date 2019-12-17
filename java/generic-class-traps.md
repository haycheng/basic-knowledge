# 关于Java泛型使用的陷阱

**问题**：对于一个泛型类，比如`GenericFoo<T>`，如果仅通过
```
GenericFoo<Bar> gf = new GenericFoo<Bar>();
```
得到对象 gf 後，在没有其他关于类型`T`信息的情况下，gf 内部能否实例化出一个`Bar`类型的对象呢？

你可能会觉得，上述语句中`GenericFoo`已经知道`T`的实际类型为`Bar`了，`GenericFoo`已经拥有了`T`的类型信息，于是就可以用`new T()`或`java.lang.Class<T>.newInstance()`构造出一个`Bar`实例来。

然而，实际上Java办不到。为什么会这样呢？

众所周知，Java为了兼容Java SE5之前的代码，其泛型是使用擦除（erasure）来实现的，即在实际运行中，类型变量将会用它的第一个限定的类型替代（如 `MyClazz<T extends Comparable & Serializable>` 中，`T`将被`Comparable`替换；若无限定则用`Object`替换）。因此，**在泛型类的内部，无法获得有关类型参数实际类型的任何信息**。

也就是说，在泛型类的内部，我们不能使用 `T.class, new T(), new T[10], obj instanceof T` 等，直接语法检查就通过不了。而对于强制类型转换 `(T)obj`，则会报 unchecked warning，但还是可以使用的（为什么这里不会被擦除呢？黑人问号……），如Java SE5之后的 `java.util.ArrayList<E>` 类，其 `E get(int index)` 方法在最终返回对象时就用了强制转型。

因此，**在泛型类的内部，无法仅凭泛型参数T来实例化一个具体`T`类型的对象**。如果泛型内部要用`T`的实例的话，一般得事先new出这样的对象，然后传给泛型对象。如果非要在泛型类内部创建类型T的对象的话，那怎么办呢？方法还是有的，那就是传入T具体的Class对象即 `T.class`，然后通过反射来创建对象，此类方法如 `java.lang.Class.newInstance()` 或 `java.lang.reflect.Array.newInstance(Class<?> componentType, int length)`（这两个方法都要求类有默认的构造方法）。

按我目前知道的，好像只有以下两个地方在运行时能用到`T`的具体类型信息：
* **在方法的入参上使用泛型`T`，将参数限定为具体的类型**
* **在方法的返回参数上使用泛型`T`，以便得到具体类型的对象（在返回需经过强制类型转换）**

