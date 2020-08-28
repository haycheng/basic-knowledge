## URL 编码

URL编码仅限于指网址中字符的编码。网络标准RFC 1738规定，URL只能使用英文字母、阿拉伯数字和某些标点符号，不能使用其他文字和符号。如果有其他特殊字符，则需要对其进行编码，然后再根据编码后的URL发起请求。


- 网址路径中特殊字符的编码，采用UTF-8，并在每个字节前加`%`来隔开。

- 查询字符串中特殊字符的编码，采用操作系统默认的编码方式（如Windows XP中文版的GB2312）。另外，对编码得到字节，不同浏览器的处理方式也不同，FireFox会用`%`隔开每个字节，IE则不会。

- 网页中的超链接或表单提交，跟HTML源码中设定的字符集（如`<head>`中的`<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">`）有关或表单`<form>`的`accept-charset`属性有关。

- 用JavaScript发起Ajax调用时，IE采用GB2312编码（XP系统的默认编码），而Firefox总是采用UTF-8编码。

由于上述几种情况，由于操作系统、浏览器、网页字符集的不同，导致URL编码方式不同，后端处理起来很麻烦。

解决方案是使用JavaScript先对URL编码，然后再向服务器提交，不要给浏览器插手的机会，保证客户端只用一种编码方式向服务器发出请求。

因为Javascript的输出总是一致的，所以就保证了服务器得到的数据是格式统一的。

Javascript语言用于编码的函数共有3个：

- `escape()`
> `escape()`很古老，已不提倡使用。`escape()`不能直接用于URL编码，它只是返回一个字符的Unicode编码值。实际上，无论网页的原始编码是什么，一旦被Javascript编码，就都变为unicode字符。也就是说，Javascipt函数的输入和输出，默认都是Unicode字符。

> 它的规则是除了ASCII字母、数字、标点符号"@ * _ + - . /"以外，对其他所有字符进行编码。在\u0000到\u00ff之间的符号被转成`%xx`的形式，其余符号被转成`%uxxxx`的形式。

> 比如，`escape("Hello World")`返回`"Hello%20World"`，`escape("春节")`返回`"%u6625%u8282"`，

> Unicode 编码的书写方式可以是`%uxxxx`或`\uxxxx`。

> `escape()`对应的解码函数是`unescape()`。

- `encodeURI()`
> `encodeURI()`是JavaScript中用来对URL编码的函数。

> 它对整个URL进行编码，除了常见的符号以外，对其他一些在网址中有特殊含义的符号"; / ? : @ & = + $ , #"，也不进行编码。

> 它编码后输出符号的UTF-8形式，并且在每个字节前加上%。

> 它对应的解码函数是`decodeURI()`。

- `encodeURIComponent()`
> 它用于对URL的组成部分进行个别编码，而不用于对整个URL进行编码。

> 因此，"; / ? : @ & = + $ , #"，这些在`encodeURI()`中不被编码的符号，在`encodeURIComponent()`中统统会被编码。

> 它对应的解码函数是`decodeURIComponent()`。

### 参考文档
1. [关于URL编码](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)
