---
title: Java类文件结构解析（二）
date: 2017-10-31 17:01:51
categories: Java
tags: 虚拟机
thumbnail: /images/javalogo.png
---

承接上文 [Java类文件结构解析（一）](http://sunshichuang.com/javabytecode/)，这里对照着字节码继续看一下字节码文件的具体结构。
从第一个16进制符往下看：

cafebabe 0000 0034 ，十进制大小为52 。表示魔数，指的是该class文件可以被java加载的版本号。  

0017 :表示这个类的常量池大小为23，那么常量池大小为22，之所以少一位，是因为0空出来特殊场景下表示不引用任何一个常量池项目。
常量池中主要存放两类常量：字面量跟符号引用。常量池中的每一项常量都是一个表。目前共有14种不同类型的表结构。且这14种表结构的首位都是一个u1类型的标志位，代表当前这个常量属于哪种常量类型。这14种常量的意思列表如下:
<!-- more -->

|	类型	|	标志	|	描述　	|
| ---  | ---  | ---   |
|	CONSTANT_utf8_info	|	1	|	UTF-8编码的字符串	|
|	CONSTANT_Integer_info	|	3	|	整形字面量	|
|	CONSTANT_Float_info	|	4	|	浮点型字面量	|
|	CONSTANT_Long_info	|	5	|	长整型字面量	|
|	CONSTANT_Double_info	|	6	|	双精度浮点型字面量	|
|	CONSTANT_Class_info	|	7	|	类或接口的符号引用	|
|	CONSTANT_String_info	|	8	|	字符串类型字面量	|
|	CONSTANT_Fieldref_info	|	9	|	字段的符号引用	|
|	CONSTANT_Methodref_info	|	10	|	类中方法的符号引用	|
|	CONSTANT_InterfaceMethodref_info	|	11	|	接口中方法的符号引用	|
|	CONSTANT_NameAndType_info	|	12	|	字段或方法的符号引用	|
|	CONSTANT_MothodType_info	|	16	|	标志方法类型	|
|	CONSTANT_MethodHandle_info	|	15	|	表示方法句柄	|
|	CONSTANT_InvokeDynamic_info	|	18	|	表示一个动态方法调用点	|

对照这个表，我们继续往下看常量池中的第一项常量是0a，表示十进制值10，查上表，是CONSTANT_Methodref_info,表示类中方法的符号索引。在下表中进一步查询该常量项的结构:

![](/images/javacodestruct.png)

在上表中查看结构是CONSTANT_Methodref_info的结构可知，表中的第一项是u1类型的标志位tag，用于区分常量类型，第二项是u2类型的索引index,第三项同样是是u2类型的index，分表表示：
     - 0006: 6,指向CONSTANT_Class_info,即常量池的第六个常量。
     - 0011: 17，常量池的第17个常量。
在[Java类文件结构解析（一）](http://sunshichuang.com/javabytecode/)文章中，我们通过javap命令拿到了详细的常量池信息，对比之下，可以知道6指向的是常量#22，也就是java/lang/Object，同理17指向的是"<init>":()V。同样的寻找方式，我们把下面的所有十六进制对应的常量池信息都列出来：


- 二:08:8,查表，字符串类型的字面量，CONSTANT_String_info

   0012 ：18，指向字符串字面量的引用

- 三：09 ：9 ，是字段的符号引用，CONSTANT_Fieldref_info

	0005
	0013

- 四：08
	0014

- 五：07，CONSTANT_Class_info

   0015
- 六：07
	0016
- 七：01 ：CONSTANT_Utf8_info
   0004 ：长度是4的字符串,查ASCII码表，分别为字符串：
   74 : t
   65 : e
   73 : s
   74 : t

- 八：01 ：CONSTANT_Utf8_info
   0012 ：长度为18字节的字符串
   4c6a6176612f6c616e672f537472696e673b ：Ljava/lang/String;

- 九：01
	0006 ：长度为6字节的字符串
	3c696e69743e ： <init>
- 十：01
	0003：长度为3字节的字符串
	282956： ()V

- 十一：01
	0004 ：长度为4字节的字符串
	436f6465 ：Code

- 十二：01
	000f：长度为15字节的字符串
	4c696e654e756d6265725461626c65：LineNumberTable

- 十三：01
	0007
	67657454657374
- 十四：01
	0014 ：20
	28294c6a6176612f6c616e672f537472696e673b ：()Ljava/lang/String;

- 十五：01
	000a
	536f7572636546696c65
- 十六：01
	0011 ：17
	4a617661436f6465546573742e6a617661

- 十七：0c ：12 ，CONSTANT_Class_info，字段或者方法的部分符号引用
	0009：指向字段或者方法名称常量项的引用，指向第九个常量，也就是<init>
	000a：指向字段或者方法描述符常量项的引用，即()V
- 十八：01
	000e：14
	6a61766120636f64652074657374 ：java code test

- 十九：0c ：12，，CONSTANT_Class_info，字段或者方法的部分符号引用
	0007：7，指向第七个常量：test
	0008：8，指向第八个常量
- 二十：01：
	0005：5个常量
	6365736869

- 二十一：01
	000c：12，
	4a617661436f646554657374
- 二十二：01
	0010 ：16个
	6a6176612f6c616e672f4f626a656374
-----以上常量池信息结束-----

紧跟着常量池结尾后的的两个字节：
- 0021 ：代表访问标志（access_flags）,用于识别类或者接口层次的访问信息；
- 0005 ：类索引，指向常量池变量5：JavaCodeTest
- 0006：父类索引，指向常量池变量：java/lang/Object，
- 0000：接口索引为0，所以没有实现接口

下面字段表信息开始：用于描述接口或类中申明的变量。
- 0001:容量计数器，fields_count=1，表示只有一个字段表数据；
- 0002：字段表的access_flags,表示字段是private；
- 0007:name_index,7,指向常量池的第七项常量：test；
- 0008：描述字段，descriptor_index:8,指向常量池的第八项常量：Ljava/lang/String;
最终得出字符串：private String test;
- 0000:attributes_count=0,表示没有额外描述的信息；
---------字段信息结束--------

下面方法表集合开始。
- 0002：容量计数器：表示类有两个方法
- 0001：access_flags=1,ACC_PUBLIC,表示public方法
- 0009： 指向常量池，方法名：<init>
- 000a:方法描述符索引：10，()V，表示特殊类型void，
- 0001：attributes_count,属性计数器1，表示方法含有一项属性；
下面属性表集合开始。
- 000b：attribute_name_index,属性的名称索引，11：Code，表示此属性是方法的字节码描述；
- 00000027：attribute_length，39
- 0002:max_stack,操作数栈的最大深度为2，
- 0001：max_locals，局部变量表所需的存储空间为1；
- 0000000b：code_length:字节码指令长度：11
- 2a:对应指令：aload_0，意思是把第一个引用类型本地变量推送到栈顶，也就是把String test 推送到栈顶；
- b7：对应指令：invokespecial,调用本对象的实例构造器方法、private方法、父类方法，后面跟一个u2类型的参数
- 0001：参数：表示调用的是哪一个方法，这里指：java/lang/Object."<init>":()V
- 2a ：aload_0
- 12 ：ldc:把int/float/String类型常量值从常量池中推送到栈顶,后面跟一个参数表示推送的常量值在常量池中的位置
- 02 ：ldc的参数，指向常量池中的第二个位置；
- b5 ：putfiled:为指定类的实例域赋值，
- 0003 ：参数，把ldc推送的常量赋值给putfiled指向的3的变量；
- b1 ：return:从当前方法返回void；


之后紧跟着的是属性计数器~~待补充
