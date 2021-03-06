## RESTful API 入门

### 什么是 RESTful
原先的软件一般是运行在单机环境中，而网络则研究的是不同系统之间的通信，二者交集不多。但随着“浏览器/服务器”模式（BS模式）的兴起，软件开发和网络在互联网环境中开始逐渐融合。越来越多的功能通过BS模式实现，RESTful架构就是目前流行的一种互联网软件架构。

RESTful一词中的REST，是Representational State Transfer的缩写，意思是资源的"表现层状态转化"。资源就是某一信息实体，可以是一段文本、一张图片，或者是一个订单。在网络上，资源可以用URI（统一资源定位符）方便地表示，访问、操作资源可以通过URI进行。

表现层（Representation）是指"资源"的外在表现形式，如文本可以通过HTML、XML或JSON形式表现出来，图片可以通过JPG或PNG形式表现。URI只代表资源，但不含它的表现形式。在HTTP请求中，资源的表现形式可以用头信息中的Accept和Content-Type字段指定，这两个字段是对"表现层"的描述。

状态转化（State Transfer）是指资源实体在客户端与服务端的互动中，产生的数据变化。这种转化需要建立在表现层之上（总需要通过某种表现形式来传达这种变化，否则用户不好操作和观察吧），所以合起来就是"表现层状态转化"。

### RESTful 实践
具体实践中，现在客户端常用的是HTTP协议。HTTP协议中用到的URL就是一种URI，它通过指定位置来确定资源；HTTP中数据的表现形式多种多样，如文本数据可以是HTML、XML或JSON形式；而用来使资源发生状态变化的操作，常见的有四个动词：GET、POST、PUT、DELETE，分别对应获取资源、新建资源（或更新资源）、更新资源、删除资源。

因此，基于HTTP协议的RESTful API的设计中，URL仅用来指定某个资源，不包含动作，对资源的操作动作是通过HTTP起始行中指定的方法指定的。资源的表现形式，客户端通过Accept来说明可接受类型，服务器则通过Content-Type来具体说明返回的数据形式。

比如，要查看用户ID为10000的余额信息，其表示方法不应该是`GET /getBalance?userId=10000`，而应该是类似于`GET /user/10000/balance`。如果用户10000要向用户10001转账99元的话，其表示方法不应该是`POST /transferBalance?fromId=10000&toId=10001&amount=99`（或将参数放在POST请求的body中）或`POST /10001/transfer/99/to/10001`，而应该是`POST /transaction?fromId=10000&toId=10001&amount=99`（或将参数放在POST请求的body中），甚至是`POST /transaction/10000/99/10001`。

总之，RESTful API的 URL 中不能出现动词，只能有表示资源的名词，比如将上述的转账操作（transfer）当作一种资源（transaction）来进行。RESTful API的动作应该通过HTTP方法来指定。

### RESTful API 常用方法（动词）说明
常用的HTTP方法（括号中是对应的SQL命令）：
> GET（SELECT）：从服务器取出资源（一项或多项）

> POST（CREATE）：在服务器新建一个资源

> PUT（UPDATE）：在服务器更新资源（客户端提供资源所有的新值）

> PATCH（UPDATE）：在服务器更新资源（客户端提供资源部分新值）

> DELETE（DELETE）：从服务器删除资源

还有两个不常用的HTTP方法是HEAD（获取资源的元数据）和OPTIONS（获取关于资源的哪些属性是客户端可以改变的）。

为了便于理解不同方法的用处与差别，看一些例子：
> GET /zoos：列出所有动物园

> GET /zoos/<id>：获取某个指定动物园的信息

> GET /zoos/<id>/animals：列出某个指定动物园的所有动物

> POST /zoos：新建一个动物园

> PUT /zoos/<id>：更新某个指定动物园的信息（提供该动物园的全部信息）

> PATCH /zoos/<id>：更新某个指定动物园的信息（提供该动物园的部分信息）

> DELETE /zoos/<id>：删除某个动物园
  
> DELETE /zoos/<zooId>/animals/<animalId>：删除某个指定动物园的指定动物

其中zoos表示动物园的集合（collection），animals表示动物的集合。

关于RESTful API的返回结果（collection表示资源集合，resource表示某个资源）：
> GET /collection：返回资源对象的列表（数组）

> GET /collection/resource：返回单个资源对象

> POST /collection：返回新生成的资源对象

> PUT /collection/resource：返回完整的资源对象

> PATCH /collection/resource：返回完整的资源对象

> DELETE /collection/resource：返回一个空文档


#### 参考文档
1. [理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)
1. [RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

