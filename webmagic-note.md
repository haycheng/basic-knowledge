# Java 爬虫 WebMagic 使用笔记

## 处理非200的页面
WebMagic对于状态码非200的页面，默认会不处理，也不会重试。
为了避免获取非200的页面而导致页面遗漏掉，用户在使用WebMagic时，需要自定义可接受的状态码，从而可以由用户自定义处理非200页面的逻辑，以便进行页面重试。

#### 参考文档
1. [WebMagic in Action](http://webmagic.io/docs/zh/)
