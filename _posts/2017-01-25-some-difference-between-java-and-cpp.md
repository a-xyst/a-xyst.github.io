---
title:  "Java与C++的一些差别"
excerpt: "寒假开始学习Java，它与C++的基础语法基本相同，在此做一些简单笔记。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Language
tags:
  - 高精度
---

寒假开始学习Java，它与C++的基础语法基本相同，在此做一些简单笔记。

## 浅层区别

1.C++的include=Java的import


2.C++的int main()=Java的public static void main(String[] args)


3.Java读取输入较为繁琐

最简单的办法是Scanner sca=new Scanner(System.in)，然后调用相应函数，例如sum=sca.nextInt()


4.C++的printf()=Java的System.out.print()

System.out.println()换行


5.C++的抽象函数=Java的abstract


6.C++的bool=Java的boolean

int和boolean不能自动转换，不能用if(a)(其中a为int)的写法。


7.对于所有函数，都要显式地声明其访问权限


8.Java的文件读写:


(1)序列化(类似于C++的二进制文件读写)

FileOutputStream fileStream=new FileOutputStreamI("out.ser");

ObjectOutputStream os=new ObjectOutputStream(fileStream);

os.writeObject(objectOne);

os.close();

读：把写的Out改成In，write改成read即可。

Java的文件读写不支持直接的字节操作，因此要使用两个流，其中一个对象转换成流，一个把流写入文件。

使用时，让相应的类implements Serializable，对于可以跳过的变量，加上transient关键词。


(2)文本化

(2.1)写序列化的对象

objectOutputStream.writeObject(objectTwo);

(2.2)写字符串

fileWriter.write(String a);

## 较大的不同


1.Java面向对象性更强

Java封装性很强，例如Main函数必须被包裹在类里面，此特点也导致其代码较功能相同的C++文件来得更长。


2.对象的建立

所有对象都存放在堆里，在最后一个指向它的引用消失时自动释放，不能直接声明对象，只能声明指向对象的引用。

例如:newtype p=new newtype();

同样，不能直接声明数组，必须以形如int []b=new int[10];的方式声明。


3.没有指针，只有引用


4.子类自动覆盖

Java的类被自动声明为虚类，继承时，自动继承在离当前类最近的父类中重声明过的的方法，可以用final防止被覆盖。


5.不存在多重继承

同一个类只能extend多个父类，但可以implement多个接口，但implement的接口中，不能有具体的实现。

更多区别有待进一步学习。
