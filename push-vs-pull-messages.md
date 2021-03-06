# 消息系统中的 push 与 pull 模式比较

在消息系统中，客户端（Consumer）从服务端（Server）获取消息的方式有两种类型：
- push（推送）：由服务端主动推送数据给客户端，通常是在有新消息到达服务端时推送。
- pull（拉取）：由客户端主动从服务端拉取数据，通常客户端会定时拉取。

从上面的描述看出，push 和 pull 是以客户端为主体来描述的，说的是客户端是通过主动的pull，还是被动的push来获取消息。

下面比较两种模式的优缺点。

特性|push模式|pull模式
-|-|-
实时性|较好，服务端收到数据后可立即发送给客户端|一般，取决于pull的间隔时间，间隔太短容易产生无效的拉取
针对性|需要服务端维护|需要服务端维护
状态维护|服务端需要保存各客户端push成功或失败的状态，客户端无需维护状态|各客户端需保存当前pull信息的状态，以便故障重启後的恢复
流量控制|服务端需要根据一定策略，对push做流量控制。若客户端无法处理消息时，无效的push对服务端有一定负载</br>解决方案：|客户端的请求可能很多无效或者没有数据可供传输，浪费带宽和服务器处理能力

## push与pull两种模式的结合使用
将信息推送与拉取两种模式结合能做到取长补短，使二者优势互补。
一般来说，先推后拉既可以拥有push的实时性，又有pull的流量控制。

#### 参考文档：
1. [阿里中间件团队博客 - Push Or Pull?](http://jm.taobao.org/2011/04/30/918/)
1. [消息系统Push/Pull模式分析](https://www.biaodianfu.com/push-pull.html)
