---
title: NIO系列3-JAVA NIO Channels
date: 2018-3-31 13:01:51
categories: NIO
tags: IO
thumbnail: /images/IO-TU.jpg
---
![](/images/IO-TU.jpg)

Java NIO Channels 跟流很相似，但是跟流又有着如下的不同点：

- 数据可以同时支持读跟写入Channels。而流通常来京只能是单向的，要不是读，要不是写。
- Channels可以支持异步的读跟写
- Channels通常都是从缓冲区Buffer中读数据，或者向Buffer中写数据

如上面提到的，通常我们都是把数据从通道中读入缓冲区，或者把数据从缓冲区写入通道.

### Channel的几种实现
在java的NIO中有如下几种重要的Channel实现：

- FileChannel
- DatagramChannel
- SocketChannel
- ServerSocketChannel
其中，FileChannel从文件中读取数据，或者向文件中写数据。
    DatagramChannel可以通过UDP协议从网络中读写数据。
    SocketChannel可以通过TCP协议从网络中读写数据。
    ServerSocketChannel允许我们监听一个TCP的连接，类似于已web服务器做的事情。对于每个新建的TCP连接，，一个SocketChannel实例都会被创建。

### 基础Channel示例
下面是一个用FileChannel读取文件到缓冲区的代码实例：
```
//开启一个读的通道
RandomAccessFile aFile = new RandomAccessFile("data/nio-data.txt", "rw");
    FileChannel inChannel = aFile.getChannel();
    //分配48byte的缓冲区用来存放数据
    ByteBuffer buf = ByteBuffer.allocate(48);
    int bytesRead = inChannel.read(buf);
    while (bytesRead != -1) {
      System.out.println("Read " + bytesRead);
      //这行代码一定要有，否则永远读不到数据，原因看下面解释
      buf.flip();
      while(buf.hasRemaining()){
          System.out.print((char) buf.get());
      }

      buf.clear();
      bytesRead = inChannel.read(buf);
    }
    aFile.close();

```
注意buf.flip()这行代码，flip调换这个buffer的当前位置，并且设置当前位置是0。意思就是：将缓存字节数组的指针设置为数组的开始序列即数组下标0。这样就可以从buffer开头，对该buffer进行遍历（读取）了。
