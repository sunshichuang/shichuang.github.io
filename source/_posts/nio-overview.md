---
title: NIO系列2-JAVA NIO 概览
date: 2018-3-30 15:01:51
categories: NIO
tags: IO
thumbnail: /images/IO-TU.jpg
---
<!-- ![](/images/IO-TU.jpg) -->

Java NIO 主要包含如下几个核心构成:

- Channels
- Buffers
- Selectors
Java NIO 的类跟组件当然远远不止于此，但是Channel、Buffer、Selector是其中核心的API。其余像Pipe和FileLock等类都仅仅是围绕这三个核心组件一起工作而已。因此，这里重点关注在这三个核心组件。对于奇特组件，将会在本系列的其他篇幅中做讲解，详细请关注本博客右侧的菜单。



## Channels and Buffers
一般情况下，在NIO中的所有IO操作都从一个通道开始。通道跟流的概念比较像，数据既可以从通道读入缓冲区，也可以从缓冲区写入通道。

Channel跟Buffer都有几种不同的实现类。以下是NIO中几种通道的主要实现类：

- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel

如上所见，这几种通道覆盖了UDP + TCP网络流和文件流。


跟这些类一起也有好几种有趣的接口，但是这篇引导文中为了简单起见，将会把这块放到本系列的其他文章中进行介绍。


以下是NIO中几种缓冲区的主要实现类：
- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer
这些缓冲区实现类基本覆盖了你可以用IO发送的基本数据类型：: byte, short, int, long, float, double and characters.
Java NIO 也有MappedByteBuffer用来处理内存映射文件。此类Buffer也将会在系列的其他章节里进行描述。


## Selectors
一个选择器允许单个线程处理多个通道实例。这个特性当你的应用有许多开着的连接，但是每个连接的使用率很低的情况时会很有用，比如聊天室。以下是一个线程用选择器处理三个通道的示例：

![](/images/select-channel.png)

在使用Selector的时候，你需要把通道注册到选择器上。然后你就可以调用选这期的select()方法，select方法会一直阻塞直到一个事件已经为某个已注册的通道准备做好准备。一旦方法返回，这个线程可以接着处理这些事件。事件是指接受数据、处理链接等等。
