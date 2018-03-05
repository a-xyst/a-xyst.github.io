---
title:  "使用java.math进行高精度运算"
excerpt: "java.math中，包含了在竞赛中实用性很强的高精度函数，以下是一些常用指令。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Language
tags:
  - 高精度
---

java.math中，包含了在竞赛中实用性很强的高精度API，以下是一些常用指令：

Scanner sca=new Scanner();
下文以sca指代用于读取的Scanner名

sca.nextBigInteger()

读取一个高进度整数

sca.nextBigDecimal()

支持小数，其他同Integer，下略

a=BigInteger.valueOf(i)

得到整数i所对应的高精度整数a

BigInteger.ZERO

高精度整数0

BigInteger.ONE

高精度整数1

a.compareTo(b)

比较两个高精度整数

a.equals(b)

判断两个高精度整数是否相等

a.add(b)/a.substract(b)/a.multiply(b)/a.divide(b)/a.remainder(b)

加减乘除;取模

a.shiftLeft(n)/a.shiftRight(n)

左移，右移n位

a.and(b)/a.or(b)/a.xor(b)/a.not(b)
与，或，异或，非

a.pow(b)

a^b

a.negate()

取负

a.gcd(b)

GCD

a.abs()

绝对值

a.bitLength()

返回该数的最小二进制补码表示的位的个数，对正数来说，这等价于普通二进制表示的位的个数。
