# 14. Java NIO vs. IO

> 原文链接：[http://tutorials.jenkov.com/java-nio/nio-vs-io.html](http://tutorials.jenkov.com/java-nio/nio-vs-io.html)

当学习Java的NIO和IO时，有个问题会跳入脑海当中：什么时候该用IO，什么时候用NIO？

下面的章节中笔者会试着分享一些线索，包括两者之间的区别，使用场景以及他们是如何影响代码设计的。

## NIO和IO之间的主要差异（Mian Differences Between Java NIO and IO）
下面这个表格概括了NIO和IO的主要差异。我们会针对每个差异进行解释。
| IO | NIO |
| -- | -- |
| Stream oriented | Buffer oriented |
| Blocking IO | No blocking IO |
| | Selectors |


## 面向流和面向缓冲区比较(Stream Oriented vs. Buffer Oriented)

## 阻塞和非阻塞IO比较（Blocking vs. No-blocking IO）

## Selectors

## NIO和IO是如何影响程序设计的（How NIO and IO Influences Application Design）

### API调用(The API Calls)

### 数据处理（The Processing of Data）

## 小结




