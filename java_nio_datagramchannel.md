# 12. Java NIO DatagramChannel


> 原文链接：[http://tutorials.jenkov.com/java-nio/datagram-channel.html](http://tutorials.jenkov.com/java-nio/datagram-channel.html)

<!-- toc -->

一个Java NIO DatagramChannel死一个可以发送、接收UDP数据包的通道。由于UDP是面向无连接的网络协议，我们不可用像使用其他通道一样直接进行读写数据。正确的做法是发送、接收数据包。

## 打开一个DatagramChannel（Opening a DatagramChannel）

打开一个DatagramChannel你这么操作：

```
DatagramChannel channel = DatagramChannel.open();
channel.socket().bind(new InetSocketAddress(9999));
```
上述示例中，我们打开了一个DatagramChannel，它可以在9999端口上收发UDP数据包。

## 接收数据（Receiving Data）
接收数据，直接调用DatagramChannel的receive()方法：
```
ByteBuffer buf = ByteBuffer.allocate(48);
buf.clear();

**channel.receive(buf);
**
```
receive()方法会把接收到的数据包中的数据拷贝至给定的Buffer中。如果数据包的内容超过了Buffer的大小。