# 04. Java NIO Buffer

> 原文链接：[http://tutorials.jenkov.com/java-nio/buffers.html](http://tutorials.jenkov.com/java-nio/buffers.html)

<!-- toc -->
Java NIO Buffers用于和NIO Channel交互。正如你已经知道的，我们从channel中读取数据到buffers里，从buffer把数据写入到channels.

buffer本质上就是一块内存区，可以用来写入数据，并在稍后读取出来。这块内存被NIO Buffer包裹起来，对外提供一系列的读写方便开发的接口。

## Basic Buffer Usage
利用Buffer读写数据，通常遵循四个步骤：

* 把数据写入buffer；
* 调用flip；
* 从Buffer中读取数据；
* 调用buffer.clear()或者buffer.compact()


## Buffer Capacity, Position and Limit
### Capacity
### Position
### Limit
## Buffer Types
## Allocating a Buffer
## Writing Data to a Buffer
## flip()
## Reading Data from a Buffer
## rewind()
## clear() and compact()
## mark() and reset()
## equals() and compareTo()
### equals()
### compareTo()