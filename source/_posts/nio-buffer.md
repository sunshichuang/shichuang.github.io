---
title: NIO系列3-JAVA NIO Buffers
date: 2018-3-31 13:01:51
categories: NIO
tags: IO
thumbnail: /images/IO-TU.jpg
---
![](/images/IO-TU.jpg)
Java NIO Buffers 用来跟NIO通道Channels湘湖协作。如前文所述，数据从通道中读入缓冲区，从缓冲区写进通道。

一个缓冲区本质上是可以往里面写入数据的内存块，写入的数据随后又可以再重新读取出来，这些内存块被NIO的Buffer对象所包装，Buffer对象提供了一些列方法让我们更容易的处理内存块中的数据。

### Buffer的基础用法
使用Buffer来处理数据的读写，通常遵循以下4个步骤：

###1.讲数据写入Buffer中
###2.调用buffer.flip()方法反转数据
###3.将数据从Buffer中读出
###4.调用buffer.clear() 或者 buffer.compact()方法。

当你向缓冲区中写入数据时，缓冲区会持续记录你已经写入的数据量。当你需要从缓冲区读取数据时，首先要把buffer从写入模式转换到读取模式，此时需要调用flip()方法反转缓冲区，实现模式的转换。在缓冲区的读取模式下，缓冲区允许你把写入的数据全部读取出来。

当所有的数据都已经读取完后，我们需要清空缓冲区，为之后的继续写入做好准备。清空缓冲区有两种实现方法：
- 调用clear()方法；
- 调用compact()方法。
这两种的方法的区别是：clear()方法会清空所有写入缓冲区的数据，而compact()仅仅清空已经被读取的那些数据，所有还未读的数据都会被移到缓冲区的开始，之后写入的新数据会紧接在这些未读数据后面。

下面的一个简单例子演示了Buffer的使用，包括一系列的write, flip, read 和 clear 操作。

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

### Buffer： Capacity, Position 以及 Limit的说明

一个buffer通常是一块可以读写的内存块。这个内存块被一个NIO的Buffer对象包裹，Buffer提供了一些列方法使得我们更容易操控这个内存块。

为了更了解Buffer的工作机制，有三个属性是我们需要很熟悉的，他们分别是：

- capacity
- position
- limit

position 和 limit 的具体意思，取决于当前Buffer的工作状态时读还是写。Capacity无论在何种场景下表达的意思都是相同的。

以下是对三个属性的一些解释说明，
Here is an illustration of capacity, position and limit in write and read modes. The explanation follows in the sections after the illustration.

 Java NIO: Buffer capacity, position and limit in write and read mode.
Buffer capacity, position and limit in write and read mode.
- Capacity
Buffer作为一个内存块，具有明确的大小，这个确定的大小我们称之为"capacity"。你可以向缓冲区中写入字符、字节等信息。只有当缓冲区的大小超过了Capacity的限制，在写更多内容前你才需要清空它(把数据读出或者清空缓冲区)


- Position
当你将数据写入缓冲区的时候，你就处于一个特定的位置。初始化的时候这个位置是0.当你将数据写入写入缓冲区，指针位置就会转移到下一个可以插入数据的单元格。Position的值最大可以变为-1。

当你从缓冲区读取数据的时候，position的值会被重置为0，这时，你读取的位置从0开始，随着数据的不断读取，指针位置一直伴随移动，读到哪里，指针位置就会指向你已读取数据的下一位。

当你从缓冲区读取数据的时候你也可以从一个给定的特定位置开始读取。当你将缓冲区从写模式置为读模式时，position的值会被重置为0。然后当你从缓冲区的position位置读取数据时，position值会提前到下个你准备读物数据的位置。


- Limit
在缓冲区的写模式下，limit的值是你还可以向缓冲区写入数据的大小。在读模式下，limit的值就等于缓冲区的大小。

因此，当缓冲区被置为写模式时，limit的值会被设置成之前读模式下的位置。换句话说，你能读出来的数据大小就是你之前写入的数据大小。


### 缓冲区类型
Java 的 NIO 有以下几种缓冲区类型:

- ByteBuffer
- MappedByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer
如你所见，这些缓冲区类型代表了不同类型的数据类型。也就是说，这些数据的缓冲区使得你的缓冲区可以处理字节、短整型、整型、长整型、浮点型、双精度型等数据类型。

MappedByteBuffer 有一点特殊，我们将在专门的地方讲解他。

### 为Buffer分配大小
当实例化一个Buffer对象的时候必须先为它分配大小。每个Buffer类都有一个allocate()来做这件事，下面是个展示如何为缓冲区类型为ByteBuffer分配一个48比特容量大小的示例：

```
ByteBuffer buf = ByteBuffer.allocate(48);
```

### 向缓冲区写入数据
有两种方式可以向缓冲区写入数据：

 - 从Channel中向Buffer写入数据
 - 自己通过缓冲区提供的put()方法向Buffer中写数据
下面的例子演示了如何从Channel中向Buffer写数据
```
public class ChannelsReadOrWriteTest {
        public static void main(String args[]){
            try{
                RandomAccessFile aFile = new RandomAccessFile("/Users/sunshichuang/Downloads/test11.txt", "rw");
                FileChannel inChannel = aFile.getChannel();

                ByteBuffer buf = ByteBuffer.allocate(48);
                //这里是从Channel向Buffer中写数据
                int bytesRead = inChannel.read(buf);
                while (bytesRead != -1) {

                    System.out.println("Read " + bytesRead);
                    buf.flip();

                    while(buf.hasRemaining()){
                        System.out.print((char) buf.get());
                    }
                    buf.clear();
                    bytesRead = inChannel.read(buf);
                }
                aFile.close();
            }catch(Exception e){
            }
        }
}
```

下面的例子演示了如何用put方法向Buffer中写数据：
```
public class ChannelsReadOrWriteTest {

        public static void main(String args[]){

            try{
                RandomAccessFile aFile = new RandomAccessFile("/Users/sunshichuang/Downloads/test11.txt", "rw");
                FileChannel inChannel = aFile.getChannel();

                IntBuffer buf = IntBuffer.allocate(48);

                buf.put(123);
                buf.flip();
                while(buf.hasRemaining()){
                    System.out.print(buf.get());
                }
                aFile.close();
            }catch(Exception e){
            }
        }
}
```  
put()方法提供了多个不同版本，使得我们可以通过多种不同的方式向Buffer中写入数据。比如，向特定的位置写入数据，或者写入字节数组类型的数据。更多的用户请参考java的doc文档。

### flip()
flip()方法将Buffer从读数据模式转入转入到写模式。调用flip()方法将Buffer的position位置重置为0，并且把limit的值设置为之前position的值。
换句话说，position现在标记是读的位置，limit标记当前写入的数据量大小或者当前缓冲区还有多少数据可以读取。
### 从缓冲区读数据
同样有两种方式可以从缓冲区读取数据。
- 把数据从缓冲区读入到通道中
- 自己从缓冲区中使用缓冲区的get()方法读数据
