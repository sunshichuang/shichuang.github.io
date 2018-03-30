---
title: NIO系列1:JAVA NIO 引导
date: 2018-3-29 19:01:51
categories: Java
tags: IO
thumbnail: /images/IO-TU.jpg
---
![](/images/IO-TU.jpg)
NIO与IO的区别


NIO的核心主要包含三个概念：

- Channels and Buffers
  在传统的标准IO的api中，一般都是用字符流或者字节流来处理数据，在NIO中，用的是另外一种概念：channels 和 buffers。数据通常从通道中读入到的缓冲区，或者从缓冲区写到通道中。

- Non-blocking IO
  Java的NIO可以让你使用非阻塞型的IO操作。比如：当一个线程请求一个通道将数据读取到缓冲区时，在通道在读取数据到缓冲区的过程中，这个线程可以去做其他事情，一旦全部数据都读取到缓冲区，这个线程可以继续执行该操作。同样对数据写入通道的操作也是如此

- Selectors
  Java NIO 有了“选择器”的概念。一个选择器就是可以管理多个通道事件（比如连接的建立、数据是否已经读取等等）的对象。因此，单个线程可以管理多个通道的数据处理。

下面的章节会详细讲一下NIO具体是如何工作的~~
