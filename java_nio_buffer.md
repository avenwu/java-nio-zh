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

当写入数据到buffer中时，buffer会记录已经写入的数据大小。当需要读数据时，通过flip()方法把buffer从写模式调整为读模式；在读模式下，可以读取所有已经写入的数据。

当读取完数据后，需要清空buffer，以满足后续写入操作。清空buffer有两种方式：调用clear()或compact()方法。clear会清空整个buffer，compact则只清空已读取的数据，未被读取的数据会被移动到buffer的开始位置，写入位置则近跟着未读数据之后。

这里有一个简单的buffer案例，包括了write，flip和clear操作：

```
RandomAccessFile aFile = new RandomAccessFile("data/nio-data.txt", "rw");
FileChannel inChannel = aFile.getChannel();

//create buffer with capacity of 48 bytes
ByteBuffer buf = ByteBuffer.allocate(48);

int bytesRead = inChannel.read(buf); //read into buffer.
while (bytesRead != -1) {

  buf.flip();  //make buffer ready for read

  while(buf.hasRemaining()){
      System.out.print((char) buf.get()); // read 1 byte at a time
  }

  buf.clear(); //make buffer ready for writing
  bytesRead = inChannel.read(buf);
}
aFile.close();
```

## Buffer Capacity, Position and Limit
buffer缓冲区实质上就是一块内存，用于写入数据，也供后续再次读取数据。这块内存被NIO Buffer管理，并提供一系列的方法用于更简单的操作这块内存。
一个Buffer有三个属性是必须掌握的，分别是：

* capacity容量
* position位置
* limit限制

position和limit的具体含义取决于当前buffer的模式。capacity在两种模式下都表示容量。
下面有张示例图，描诉了不同模式下position和limit的含义：

![buffers-modes.png](http://tutorials.jenkov.com/images/java-nio/buffers-modes.png)
**Buffer capacity, position and limit in write and read mode.**

### Capacity
作为一块内存，buffer有一个固定的大小，叫做capacity容量。也就是最多只能写入容量值得字节，整形等数据。一旦buffer写满了就需要清空已读数据以便下次继续写入新的数据。
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