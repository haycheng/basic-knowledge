# 字符编码

## URL编码
URL Encode又称为“百分号编码”，它主要用来在URI里面将特殊字符进行转义，因为像/、&、=等等这类字符在URI里面本身是有功能性的。

对于ASCII字符的编码很简单就是用%后跟ASCII编码的16进制表示，例如/的ASCII 码值是47，16进制表示是2F，于是它的URL Encode结果就是%2F。

对于非ASCII字符，将它的每个字节进行相同规则的转换，例如中文“编码”的Unicode char code是U+7F16 7801，UTF-8编码的字节序列是E7 BC 96 E7 A0 81，所以它按照UTF-8编码的URL Encode结果就是%E7%BC%96%E7%A0%81。

可以看出，URL Encode编码非ASCII字符的时候，结果与使用的字符编码有关。因此在页面上提交表单、发起Ajax请求等操作的时候需要注意编码。浏览器会按照当前页面所使用的字符编码对表单体提交进行URL Encode，但使用JavaScript的encodeURI和encodeURIComponent的时候则总是会使用UTF-8（参考MDN）。

表单提交的时候编码是非常重要的，一旦错了服务端解开数据的时候就会跪。比如Github在它们的搜索表单里面放了一个<input name="utf8" type="hidden" value="✓">，其中那个对钩✓是U+2713，UTF-8编码是E2 9C 93，他们可以在服务端检测这个参数的值对不对从而对URL里用的编码进行一个初步检测。虽然我没有看到他们使用其他编码的情况，不过这样也算是一个编码协商和Check的手段吧。

在JavaScript中使用escape也可以达到URL Encode的效果，但是它对于非ASCII字符使用了一种非标准的的实现，例如“编码”会被escape成%u7F16%u7801这种%uxxxx奇怪的表示，W3C把这个函数废弃了，身为一名前端还用是打脸的哦。

### 空格的URL编码
URL中的空格在有些地方被编码成%20，另一些地方则被编码成加号+，为什么会出现这种情况？这两种编码有什么区别呢？

一个URL的基本组成部分包括协议、域名、端口号、路径和查询字符串（暂不考虑路径参数和锚点标记），路径和查询字符串之间用问号?隔开。例如"http://www.example.com/index?param=1", 路径为index，查询字符串(Query String)为param=1。URL中关于空格的编码正是与空格所在位置相关：**空格被编码成加号+的情况只会在查询字符串部分出现，而被编码成%20则可以出现在路径和查询字符串中**。

之所以有这种不同的编码方式，是由以下两份规定造成的：

1）W3C标准规定（RFC1738），当`Content-Type`为`application/x-www-form-urlencoded`时，URL中**查询字符串的参数名和参数值中空格用加号+替代**。因此使用该规范的浏览器在表单提交后，URL查询参数中空格都会被编成加号+。

2）在另一份规范（RFC2396，定义URI）里, URI里的保留字符都需转义成%HH格式(Section 3.4 Query Component)，因此空格会被编码成%20，加号+本身也作为保留字而被编成%2B。对于某些遵循RFC2396标准的应用来说，它可能不接受URL中出现加号+，认为它是非法字符。

所以一个保险的做法是，**在URL中统一使用%20来编码空格字符**。

Java中的`URLEncoder`（`java.net.URLEncoder`）是用来把字符串编码成`application/x-www-form-urlencoded`MIME 格式的字符串，也就是说仅适用于**URL中的查询字符串部分**。但是`URLEncoder`经常被用来对URL的其他部分编码，它的encode方法会把所有加号+都编码成%2B，把所有空格都编码成加号+（编码后的字符串中既有%2B，也有加号+）。与之对应的是，`URLDecoder`（java.net.URLDecoder）的`decode`方法会把加号+和%20都解码为空格。因此为了确保不出错，在调用`URLEncoder.encode`对URL进行编码后（原有的加号+已被编码成%2B，原有的空格被编码成加号+），再调用`String#replaceAll("\\+", "%20")`，将所有加号+替换为%20。

## Base64编码
Base64编码是一种用可见字符表示二进制数据的方法。它用了64个可见字符[A-Za-z0-9+/]。

Base64的编码程序非常简单，由于64=2^6，6和8的最小公倍数是24，也就是3byte，因此对输入数据以3byte为一个单位，查表把它转换成4个可见字符。

如果输入末尾不足3byte，那就补足，补1个byte就在输出末尾添加一个=，补2个byte同理。

Base64经常用来在一些文本协议里面保存二进制数据，比如HTTP协议，或者电子邮件的附件啊什么的。同时因为它的输出对于人类而言不可读，可以起到一些“混淆加密”的作用，事实上就有修改64个字符的排布来做一个变形Base64实现一个简单加密算法的例子。从密码学的角度看它基本上没什么强度可言，但是足够简单，可以起到防君子不防小人的作用。

由于一个字符只能编码6bit，自身却占了8bit，8/6=1.33，因此使用Base64来表示数据的时候会浪费1/3的体积。对于在CSS里面用Base64的data-url方式表示图片，用之前不妨简单估算一下，膨胀的体积和一个HTTP请求头比起来会相差多少，说不定涨太多了已经损失掉省一个请求的收益了。

## 乱码问题


### 参考文档：
1. [编码歪传——番外篇](http://jimliu.net/2015/03/07/something-about-encoding-extra/)
1. [URL中关于空格的编码(转)](https://blog.csdn.net/foxpeter/article/details/23475293)
1. [B.2.1 Non-ASCII characters in URI attribute values](https://www.w3.org/TR/html40/appendix/notes.html#h-B.2.1)
1. [RFC 1738 - Uniform Resource Locators (URL)](http://www.faqs.org/rfcs/rfc1738.html)
1. [RFC 2396 - Uniform Resource Identifiers (URI): Generic Syntax](http://www.faqs.org/rfcs/rfc2396.html)
