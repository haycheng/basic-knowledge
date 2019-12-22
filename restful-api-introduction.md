## RESTful API 入门

### 什么是 RESTful
原先的软件一般是运行在单机环境中，而网络则研究的是不同系统之间的通信，二者交集不多。但随着“浏览器/服务器”模式（BS模式）的兴起，软件开发和网络在互联网环境中开始逐渐融合。越来越多的功能通过BS模式实现，RESTful架构就是目前流行的一种互联网软件架构。

RESTful一词中的REST，是Representational State Transfer的缩写，意思是资源的"表现层状态转化"。资源就是某一信息实体，可以是一段文本、一张图片，或者是一个订单。在网络上，资源可以用URI（统一资源定位符）方便地表示，访问、操作资源可以通过URI进行。

表现层（Representation）是指"资源"的外在表现形式，如文本可以通过HTML、XML或JSON形式表现出来，图片可以通过JPG或PNG形式表现。URI只代表资源，但不含它的表现形式。在HTTP请求中，资源的表现形式可以用头信息中的Accept和Content-Type字段指定，这两个字段是对"表现层"的描述。

状态转化（State Transfer）是指资源实体在客户端与服务端的互动中，产生的数据变化。这种转化需要建立在表现层之上（总需要通过某种表现形式来传达这种变化，否则用户不好操作和观察吧），所以合起来就是"表现层状态转化"。

### RESTful 实践
具体实践中，现在客户端常用的是HTTP协议。HTTP协议中用到的URL就是一种URI，它通过指定位置来确定资源；HTTP中数据的表现形式多种多样，如文本数据可以是HTML、XML或JSON形式；而用来使资源发生状态变化的操作，常见的有四个动词：GET、POST、PUT、DELETE，分别对应获取资源、新建资源（或更新资源）、更新资源、删除资源。

因此，基于HTTP协议的RESTful API的设计中，URL仅用来指定某个资源，不包含动作，对资源的操作动作是通过HTTP起始行中指定的方法指定的。资源的表现形式，客户端通过Accept来说明可接受类型，服务器则通过Content-Type来具体说明返回的数据形式。

比如，要查看用户ID为10000的余额信息，其表示方法不应该是`GET /getBalance?userId=10000`，而应该是类似于`GET /user/10000/balance`。如果用户10000要向用户10001转账99元的话，其表示方法不应该是`POST(或GET) /transferBalance?fromId=10000&toId=10001&amount=99`（或将参数放在POST请求的body中）或`POST /10001/transfer/99/to/10001`，而应该是`POST /transaction?fromId=10000&toId=10001&amount=99`（或将参数放在POST请求的body中），甚至是`POST /transaction/10000/99/10001`。总之，RESTful API的 URL 中不能出现动词，只能有表示资源的名词，比如将上述的转账操作（transfer）当作一种资源（transaction）来进行。


### 参考文档
1. [理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)
1. [RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
