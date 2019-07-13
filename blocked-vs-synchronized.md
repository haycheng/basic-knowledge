# IO

## 阻塞、非阻塞 与 同步、异步 的含义

- 阻塞与非阻塞，是描述调用方的。比如说，调用方调用某个方法，然后调用方被阻塞了，或者不阻塞，马上就返回了。
- 同步与异步，是描述被调用方的。比如说，某个被调用方法会同步地执行某个请求，或者被调用方法只是将请求加到任务队列中去，后面会异步地去执行。

一般来说，同步与阻塞常一起出现，而异步与非阻塞常一起出现。

## 几种IO缩写
- BIO：Blocking IO，即阻塞IO。在Java中，BIO的实现位于java.io包下。

- NIO：有两种含义。一是 Non-blocking IO，即非阻塞IO；另一种是 New IO，是Java 1.4 开始引入的一种新的IO方式，在java.nio包下。Java 的 New IO 是面向块的IO，而Java 1.4之前的IO是面向流的。New IO只支持阻塞式调用。

- AIO：Asynchronized IO，即异步IO。

## 几种IO模型
### 阻塞IO

### 非阻塞IO

**参考文档**
1. [几种IO的比较](https://github.com/hollischuang/toBeTopJavaer/blob/master/basics/java-basic/bio-vs-nio-vs-aio.md)

