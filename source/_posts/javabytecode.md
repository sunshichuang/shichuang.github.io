---
title: javabytecode
date: 2017-10-25 17:01:51
categories: Java
tags: 字节码
thumbnail: /images/javabytecode.png
---

作为一个Java开发，每天编写的Java代码都是成百上千行，略去枯燥的业务代码不提，追寻代码从编译成class文件开始，虚拟机如何加载、解析这些代码的过程，一定要比单纯写业务代码有意思的多，最起码，要比遇到时间转化，去谷歌一个日期转化函数代码，粘贴了事，来的有意义多啊。哈哈哈~~

# 一、就从class文件开始吧
我们日常开发的过程大致是这样的，我要提供个某某功能的服务，所以，我们开始巴拉巴拉、、

``` java
public class AaBaServiceImpl implements AaBaService {
   //巴拉巴拉一堆代码：服务依赖、变量定义、方法定义、常量、日志 等等等
}

```
<!-- more -->
是不是机械的很，熟练的不行啊，快飞起来了。瞬间觉得自己狂拽掉咋天。BUT FUCK为啥我非要写成这样？我可是程序员啊 ，创造力无限的说，咋就不能一朝顿悟，一掌辟出个黯然销魂掌？答曰：那必须不能啊，因为神雕侠侣特么它不是你写的啊。类比可知：Java特么也不是你发明的，人家的规范早就定义好了，在这个基础规范里面，文件结构那只能这么搞，为了一个崇高的“梦想”：师夷长技以制夷。万一哪天牛逼了，一不小心独创了个语言呢？人家杨过那也是厚积薄发，内力深厚，阅经沧桑，不然还销魂啥啊，没等销魂，就被蛤蟆功撑死了。所以，先积一下。来看看这个[虚拟机规范](http://docs.oracle.com/javase/specs/jvms/se7/html/index.html) ，章节翻到第四节：Chapter 4. The class File Format，类文件格式：

```
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    u2             constant_pool_count;
    cp_info        constant_pool[constant_pool_count-1];
    u2             access_flags;
    u2             this_class;
    u2             super_class;
    u2             interfaces_count;
    u2             interfaces[interfaces_count];
    u2             fields_count;
    field_info     fields[fields_count];
    u2             methods_count;
    method_info    methods[methods_count];
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}

```

这里u4、u2表示的4个字节，2个字节的意思。但是这么看起来还是很抽象，不明觉厉。那先画个图给他串一下，顺便，把这几个英文的意思也标记一下：

![](/images/javabytecode.png)
对照着图，分别简单聊一下这些class文件的结构内容：

- magic：魔数。值是十六进制：CAFEBABE。作用是标记文件的身份，也就是标记该文件是否可以被虚拟机接受。

- minor_version: 次版本号。
- major_version：主版本号。

这两哥们决定了类文件版本格式。简单的说，就是决定类文件可以被哪个版本的虚拟机执行。假设minor_version的值是M，major_version的值是m，那么这个类的版本号就是M.m。

- constant_pool_count：常量池统计数。表示常量池的大小。

- constant_pool：常量池。大小是constant_pool_count-1。常量池的主要内容是字面量和符号引用。

- access_flags：访问标志。表示类或者接口的访问信息。比如是Class还是Interface，定义域是不是public，Class是不是抽象的等。

- this_class: 类索引。标记这个类的全量名，指向常量值的字面量。
- super_class: 父类索引。标记类父类的全量名。
- interfaces_count：接口计数器。表示接口的数量。
- interfaces：具体的接口信息。
- fields_count：字段信息计数器，用来描述接口或者类中的声明的变量。
- fields: 字段的具体信息。
- methods_count：方法表计数器。
- methods：方法表具体信息。
- attributes_count：属性表计数器。
- attributes：属性表具体信息。



Test：

Compiled from "JavaCodeTest.java"
public class JavaCodeTest {
  public JavaCodeTest();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: ldc           #2                  // String java code test
       7: putfield      #3                  // Field test:Ljava/lang/String;
      10: return

  public java.lang.String getTest();
    Code:
       0: ldc           #4                  // String ceshi
       2: areturn
}

2.查看更详细的信息，输出常量表信息：javap -verbose + 类名称

Classfile /Users/sunshichuang/Desktop/personal_file/workTest/workTest/Java_Code/src/JavaCodeTest.class
  Last modified 2017-6-12; size 359 bytes
  MD5 checksum dd09242d3d1869220c01d5976e503de2
  Compiled from "JavaCodeTest.java"
public class JavaCodeTest
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#17         // java/lang/Object."<init>":()V
   #2 = String             #18            // java code test
   #3 = Fieldref           #5.#19         // JavaCodeTest.test:Ljava/lang/String;
   #4 = String             #20            // ceshi
   #5 = Class              #21            // JavaCodeTest
   #6 = Class              #22            // java/lang/Object
   #7 = Utf8               test
   #8 = Utf8               Ljava/lang/String;
   #9 = Utf8               <init>
  #10 = Utf8               ()V
  #11 = Utf8               Code
  #12 = Utf8               LineNumberTable
  #13 = Utf8               getTest
  #14 = Utf8               ()Ljava/lang/String;
  #15 = Utf8               SourceFile
  #16 = Utf8               JavaCodeTest.java
  #17 = NameAndType        #9:#10         // "<init>":()V
  #18 = Utf8               java code test
  #19 = NameAndType        #7:#8          // test:Ljava/lang/String;
  #20 = Utf8               ceshi
  #21 = Utf8               JavaCodeTest
  #22 = Utf8               java/lang/Object
{
  public JavaCodeTest();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: ldc           #2                  // String java code test
         7: putfield      #3                  // Field test:Ljava/lang/String;
        10: return
      LineNumberTable:
        line 4: 0
        line 5: 4

  public java.lang.String getTest();
    descriptor: ()Ljava/lang/String;
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: ldc           #4                  // String ceshi
         2: areturn
      LineNumberTable:
        line 8: 0
}
SourceFile: "JavaCodeTest.java"

3.命令行查看十六进制的class文件：
  1）首先以二进制方式编辑这个文件：vi -b datafile
   2）使用xxd转换为16进制：:%!xxd
  3）转换16进制回来vi：:%!xxd -r


4.十六进制内容：

cafebabe 0000 0034   


0017 :常量池大小为23，表示常量池大小为22，0空出来特殊场景下表示：不引用任何一个常量池项目

第一个常量：0a:值是10，查表，是CONSTANT_Methodref_info,表示表示类中方法的符号索引:结构是u1tag，u2index,u2index

         0006: 6,指向CONSTANT_Class_info,即常量池的第六个常量，
         0011: 17，常量池的第17个常量，
二:08:8,查表，字符串类型的字面量，CONSTANT_String_info

   0012 ：18，指向字符串字面量的引用

三：09 ：9 ，是字段的符号引用，CONSTANT_Fieldref_info

	0005
	0013

四：08
	0014

五：07，CONSTANT_Class_info

   0015

六：07
	0016
七：01 ：CONSTANT_Utf8_info
   0004 ：长度是4的字符串,查ASCII码表，分别为字符串：
   74 : t
   65 : e
   73 : s
   74 : t

八：01 ：CONSTANT_Utf8_info
   0012 ：长度为18字节的字符串
   4c6a6176612f6c616e672f537472696e673b ：Ljava/lang/String;

九：01
	0006 ：长度为6字节的字符串
	3c696e69743e ： <init>
十：01
	0003：长度为3字节的字符串
	282956： ()V

十一：01
	0004 ：长度为4字节的字符串
	436f6465 ：Code

十二：01
	000f：长度为15字节的字符串
	4c696e654e756d6265725461626c65：LineNumberTable

十三：01
	0007
	67657454657374
十四：01
	0014 ：20
	28294c6a6176612f6c616e672f537472696e673b ：()Ljava/lang/String;

十五：01
	000a
	536f7572636546696c65
十六：01
	0011 ：17
	4a617661436f6465546573742e6a617661

十七：0c ：12 ，CONSTANT_Class_info，字段或者方法的部分符号引用
	0009：指向字段或者方法名称常量项的引用，指向第九个常量，也就是<init>
	000a：指向字段或者方法描述符常量项的引用，即()V
十八：01
	000e：14
	6a61766120636f64652074657374 ：java code test

十九：0c ：12，，CONSTANT_Class_info，字段或者方法的部分符号引用
	0007：7，指向第七个常量：test
	0008：8，指向第八个常量
二十：01：
	0005：5个常量
	6365736869

二十一：01
	000c：12，
	4a617661436f646554657374
二十二：01
	0010 ：16个
	6a6176612f6c616e672f4f626a656374

-----常量池结束-----
紧跟着的两个字节：
0021 ：代表访问标志（access_flags）,用于识别类或者接口层次的访问信息；

0005 ：类索引，指向常量池变量5：JavaCodeTest

0006：父类索引，指向常量池变量：java/lang/Object，
0000：接口索引为0，没有实现接口

---------字段表信息开始：用于描述接口或类中申明的变量--------
0001:容量计数器，fields_count,1，表33333333333333333333333333333示只有一个字段表数据
0002：字段表的access_flags,表示字段是private
0007:name_index,7,指向常量池的第七项常量：test，
0008：描述字段，descriptor_index:8,指向常量池的第八项常量：Ljava/lang/String;

			得出：private String test;
0000:attributes_count=0,表示没有额外描述的信息；

---------字段信息结束--------

---------方法表集合开始----------
0002：容量计数器：表示有两个方法

0001：access_flags:1,ACC_PUBLIC,表示public方法
0009： 指向常量池，方法名：<init>
000a:方法描述符索引：10，()V，表示特殊类型void，
0001：attributes_count,属性计数器1，表示方法含有一项属性；
   ------属性表集合开始---
000b：attribute_name_index,属性的名称索引，11：Code，表示此属性是方法的字节码描述；
00000027：attribute_length，39
0002:max_stack,操作数栈的最大深度为2，
0001：max_locals，局部变量表所需的存储空间为1；
0000000b：code_length:字节码指令长度：11
2a:对应指令：aload_0，意思是把第一个引用类型本地变量推送到栈顶，也就是把String test 推送到栈顶；
b7：对应指令：invokespecial,调用本对象的实例构造器方法、private方法、父类方法，后面跟一个u2类型的参数
0001：参数：表示调用的是哪一个方法，这里指：java/lang/Object."<init>":()V
2a ：aload_0
12 ：ldc:把int/float/String类型常量值从常量池中推送到栈顶,后面跟一个参数表示推送的常量值在常量池中的位置
02 ：ldc的参数，指向常量池中的第二个位置；
b5 ：putfiled:为指定类的实例域赋值，
0003 ：参数，把ldc推送的常量赋值给putfiled指向的3的变量；
b1 ：return:从当前方法返回void；

0000 ：exception_table_length
0001 :attributes_count：
000c ：attribute_name_index:12,指向常量池中的LineNumberTable
0000000a ：

0002
00000004000400050001000d000e0001000b0000001b0001000100000003
1204b000000001000c000000060001000000080001000f000000020010
