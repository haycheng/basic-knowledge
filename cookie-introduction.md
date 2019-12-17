# Cookie 入门

## Cookie 基础

HTTP 是无状态协议，即服务器无法识别出哪些请求是属于同一个浏览器（用户）发出的。即使 HTTP1.1 支持长连接，但如果长时间没有与服务端通信，连接也会被自动关闭。

因此需要想办法判断多次请求是否为同一个浏览器发起的，即为一个会话（Session）。Cookie 就是 HTTP 协议中用来识别用户的一种方式。

Cookie 由服务端在 HTTP 返回结果中设置，然后传送给客户端浏览器。浏览器收到 Cookie 后，会保存起来，并（可能）在下一次发起请求时，带上该Cookie。服务端处理该请求时，会检查是否带有Cookie，若存在则取出，并用作业务处理。

## HTTP 协议中的 Cookie


## Java 中的 Cookie
Java 中的 Cookie 类是`javax.servlet.http.Cookie`.


### 参考文档
1. [JavaWeb Cookie详解](https://www.cnblogs.com/demodashi/p/9436568.html)

