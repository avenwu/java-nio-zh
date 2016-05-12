# 11. Java NIO: Non-blocking Server

> 原文链接：[http://tutorials.jenkov.com/java-nio/non-blocking-server.html#non-blocking-vs-blocking-io-pipelines](http://tutorials.jenkov.com/java-nio/non-blocking-server.html#non-blocking-vs-blocking-io-pipelines)

<!-- toc -->

现在我们已经知道了Java NIO里面那些非阻塞特性是怎么工作的，但是要设计一个非阻塞的服务仍旧比较困难。非阻塞IO相对传统的阻塞IO给开发者带来了更多的挑战。在本节非阻塞服务的讲解中，我们一起来讨论这些会面临的主要挑战，同时也会给出一些潜在的解决方案。

查找关于设计非阻塞服务的相关资料是比较难的，本文提出的解决方案也只能是基于笔者个人的工作经验，构思。如果你有其他的解决方案或者是更好的点子，那么还请不吝赐教。你可以在文章下方的评论区回复，或者可以给我发送邮件，也可以直接在[Twitter](https://twitter.com/jjenkov)上联系我。

虽然本文介绍的一些点子是为Java NIO设计的，但是我相信这些思路同样适用于其他编程语言，只要他们也存在和Selector类似结构，概念。就目前我的了解来说，这些结构底层OS提供的，所以基本上你可以运用到其他编程语言中去。
## 非阻塞服务-GitHub源码仓（Non-blocking Server - GitHub Repository）

为了演示本文探讨的一些技术，笔者已经在GitHub上面建立了相应的源码仓，地址如下：
[https://github.com/jjenkov/java-nio-server](https://github.com/jjenkov/java-nio-server)
## 非阻塞IO通道（Non-blocking IO Pipelines）
非阻塞的IO管道（Non-blocking IO Pipelines）可以看做是整个非阻塞IO处理过程的链条。包括在以非阻塞形式进行的读与写操作。下面有一张插图，简单的描述了一个基础的非阻塞的IO管道（Non-blocking IO Pipelines）：
![non-blocking-server-1](http://tutorials.jenkov.com/images/java-nio/non-blocking-server-1.png)
我们的组件（Component）通过Selector检查当前Channel是否有数据需要写入。此时component读入数据，并且根据输入的数据input对外提供数据输出output。这个对外的数据输出output被写到了另一个Channel中。

一个非阻塞的IO管道不必同时需要读和写数据，通常来说有些管道只需要读数据，而另一些管道则只需写数据。
上面的这幅流程图仅仅展示了一个组件。实际上一个管道可能存在多个component在处理输入数据。管道的长度取决于管道具体要做的事情。

当然一个非阻塞的IO管道他也可以同时从多个Channel中读取数据，例如同时冲多个SocketChannel中读取数据；
上面的流程图实际上被简化了，图中的Component实际上负责初始化Selector，从Channel中读取数据，而不是由Channel往Selector压如数据（push），这是简化的上图容易给人带来的误解。

## 非阻塞和阻塞通道比较（Non-blocking vs. Blocking IO Pipelines）
非阻塞IO管道和阻塞IO管道之间最大的区别是他们各自如何从Channel（套接字socket或文件file）读写数据。
IO管道通常直接从流中（来自于socket活file的流）读取数据，然后把数据分割为连续的消息。这个处理与我们读取流信息，用tokenizer进行解析非常相似。不同的是我们在这里会把数据流分割为更大一些的消息块。我把这个过程叫做Message Reader.下面是一张说明的插图：
![non-blocking-server-2.png](http://tutorials.jenkov.com/images/java-nio/non-blocking-server-2.png)
一个阻塞IO管道的使用可以和输入流一样调用，每次从Channel中读取一个字节的数据，阻塞自身直到有数据可读。这个流程就是一个阻塞的Messsage Reader实现。

使用阻塞IO大大简化了Message Reader的实现成本。阻塞的Message Reader无需关注没有数据返回的情形，无需关注返回部分数据或者数据解析需要被复用的问题。
相似的，一个阻塞的Message Writer也不需要关注写入部分数据，和数据复用的问题。

### 阻塞IO通道的缺点（Blocking IO Pipeline Drawbacks

）
上面提到了阻塞的Message Reader易于实现，但是阻塞也给他带了不可避免的缺点，必须为每个数据数量都分配一个单独线程。原因就在于IO接口在读取数据时在有数据返回前会一直被阻塞住。这直接导致我们无法用单线程来处理一个流没有数据返回时去读取其他的流。每当一个线程尝试去读取一个流的数据，这个线程就会被阻塞直到有数据真正返回。

如果这样的IO管道运用到服务器去处理高并发的链接请求，服务器将不得不为每一个到来的链接分配一个单独的线程。如果并发数不高比如每一时刻只有几百并发，也行不会有太大问题。一旦服务器的并发数上升到百万级别，这种设计就缺乏伸缩性。每个线程需要为堆栈分配320KB（32位JVM）到1024KB(64位JVM)的内存空间。这就是说如果有1,000,000个线程，需要1TB的内存。而这些在还没开始真正处理接收到的消息前就需要（消息处理中还需要为对象开拍内存）。

为了减少线程数，很多服务器都设计了线程池，把所有接收到的请求放到队列内，每次读取一条连接进行处理。这种设计可以用下图表示：
![non-blocking-server-3.png](http://tutorials.jenkov.com/images/java-nio/non-blocking-server-3.png)
但是这种设计要求缓冲的连接进程发送有意义的数据。如果这些连接长时间处于非活跃的状态，那么大量非活跃的连接会阻塞线程池中的所有线程。这会导致服务器的响应速度特别慢甚至无响应。

有些服务器为了减轻这个问题，采取的操作是适当增加线程池的弹性。例如，当线程池所有线程都处于饱和时，线程池可能会自动扩容，启动更多的线程来处理事务。这个解决方案会使得服务器维护大量不活跃的链接。但是需要谨记服务器所能开辟的线程数是有限制的。所有当有1,000,000个低速的链接时，服务器还是不具备伸缩性。

## 基础的非阻塞通道设计（Basic Non-blocking IO Pipeline Design）
一个非阻塞的IO通道可以用单线程读取多个数据流。这个前提是相关的流可以切换为非阻塞模式（并不是所有流都可以以非阻塞形式操作）。在非阻塞模式下，读取一个流可能返回0个或多个字节。如果流还没有可供读取的数据那么就会返回0，其他大于1的返回都表明这是实际读取到的数据；

为了避开没有数据可读的流，我们结合Java NIO中的Selector。一个Selector可以注册多个SelectableChannel实例。当我们调用select()或selectorNow()方法时Selector会返回一个有数据可读的SelectableChannel实例。这个设计可以如下插图：
![non-blocking-server-4.png](http://tutorials.jenkov.com/images/java-nio/non-blocking-server-4.png)
## 读取部分信息(Reading Partial Messages)
当我们冲SelectableChannel中读取一段数据后，我们并不知道这段数据是否是完整的一个message。因为一个数据段可能包含部分message，也就是说即可能少于一个message，也可能多一个message，正如下面这张插图所示意的那样：
![non-blocking-server-5.png](http://tutorials.jenkov.com/images/java-nio/non-blocking-server-5.png)
要处理这种截断的message，我们会遇到两个问题：
  
1. 检测数据段中是否包含一个完整的message
2. 在message剩余部分获取到之前，我们如何处理不完整的message

检测完整message要求Message Reader查看数据段中的数据是否至少包含一个完整的message。如果包含一个或多个完整message，这些message可以被下发到通道中处理。查找完整message的过程是个大量重复的操作，所以这个操作必须是越快越好的。

当数据段中有一个不完整的message时，无论不完整消息是整个数据段还是说在完整message前后，这个不完整的message数据都需要在剩余部分获得前存储起来。

检查message完整性和存储不完整message都是Message Reader的职责。为了避免混淆来自不同Channel的数据，我们为每一个Channel分配一个Message Reader。整个设计大概是这样的：
![non-blocking-server-6.png](http://tutorials.jenkov.com/images/java-nio/non-blocking-server-6.png)
当我们通过Selector获取到一个有数据可以读取的Channel之后，改Channel关联的Message Reader会读取数据，并且把数据打断为Message块。得到完整的message后就可以通过通道下发到其他组件进行处理。

一个Message Reader自然是协议相关的。他需要知道message的格式以便读取。如果我们的服务器是跨协议复用的，那他必须实现Message Reader的协议-大致类似于接收一个Message Reader工厂作为配置参数。

## 存储不完整的Message（Storing Partial Messages）



