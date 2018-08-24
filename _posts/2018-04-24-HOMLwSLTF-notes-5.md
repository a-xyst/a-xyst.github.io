---
title:  "HOMLwSLaTF第五章阅读笔记"
excerpt: "第五章 Support Vector Machine"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  - SVM
---

本章主要介绍SVM(Support Vector Machine, 支持向量机).

## 线性SVM分类

对于线性可分的两个类别, SVM模型找出一条将它们分开的直线. SVM的优化目的是: 在保证将这两个类别里的数据项分开的同时, 让分别在两个类别中离这条线最近的数据项之间的距离最小化. 可知SVM所学习的决策边界实际上完全由两个离其最近的数据项决定, 它们被称为支持向量.

### 软边界分类

硬边界分类意味着决策边界必须将两个类别中的所有数据项完全分开, 不得出现误判. 这在实际数据集中常常是难以达成的(考虑线性不可分数据/离群点), 因此有软边界分类, 即只要误分类点和决策边界之间的距离不超过一定值即可. 对于过拟合的模型, 可以尝试增大软边界距离.

使用sklearn.svm的LinearSVC训练一个线性SVM, C值越小说明软边界距离越大.

也可以使用SGDClassifier(loss="hinge",alpha=1/(m*C)). SVC不好, 因为速度太慢.

## 非线性SVM分类

可以通过增加多项式特征数使部分线性不可分数据集变得线性可分, 体现在图上, 就是增加分界线的数量. 通过sklearn的PolynomialFeatures可以做到这一点.

使用例, 注意degree过高可导致过拟合:

```python
 Pipeline((
   ("poly_features",PolynomialFeatures(degree=3)),("scaler", StandardScaler()), ("svm_clf", LinearSVC(C=10, loss="hinge"))
   ))
```
### 多项式Kernel

如果线性不可分数据集的特征数特别多, 直接增加特征数计算内积的成本过大, 需要使用Kernel函数把数据映射到高维空间中, 从而在高维空间中使用线性模型对数据进行分类. [详细说明](https://en.wikipedia.org/wiki/Positive-definite_kernel)

使用例:

```python
 Pipeline((
   ("scaler", StandardScaler()), ("svm_clf", LinearSVC(kernel="poly", degree=3, coef0=1, C=5))
   ))
```

寻找合适超参数的常用方法是网格搜索, 先粗略搜, 再缩小搜索范围.

### 增加相似度特征

另外一个为线性不可分数据集增加特征数的方法是使用相似函数增加特征数.

例: 对于一个一维的数据集, 取x=-2, x=1的2个数据点作为界标(即为数据集增加2个新特征), 对于任意一个点, 根据对应相似函数的输出值确定它的2个新特征, 其中相似函数的输入是当前数据点与所选界标的x值.

### Gaussian RBF Kernel

在大数据集上, 逐个计算新特征是不可行的. 与增加多项式特征时同理, 可以利用Kernel函数简化计算.

使用例:

```python
 Pipeline((
   ("scaler", StandardScaler()), ("svm_clf", SVC(kernel="rbf", gamma=5, C=0.001))
   ))
```

同样, 过拟合时应考虑缩小gamma.

### 计算复杂度

Class | Time complexity | Out-of-core support | Scaling required | Kernel trick
LinearSVC | O(mn) | No | Yes | No
SGDClassifier | O(mn) | Yes | Yes | No
SVC | O(m^2n)~O(m^3n) | No | Yes | Yes

## SVM回归

除了线性与非线性分类, SVM也支持线性与非线性回归.

SVM的任务从分隔两个类别的数据点变为生成一个尽可能地拟合数据点的回归模型. 同样, SVM回归也支持软边界, 由超参数epsilon控制, 它与边界宽度成正比. 可以通过sklearn.svm的LinearSVR实现SVM回归.

## SVM详解

生病一直没好, 好像还在低烧, 等身体好点再更新...

### 决策函数和预测

### 训练目标

### 二次规划

### Kernelized SVM

### 在线SVM

