---
title:  "开始使用kaggle"
excerpt: "掌握了基本操作。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  - 机器学习
  - kaggle
---

Kaggle是一个数据科学竞赛平台，我去年曾经注册过，但由于时间不足&搞不懂基本操作（教程上来就让人去做Titanic数据集，不太友好），看了点Titanic的介绍后就扔到一边了。

昨天看到HOMLwSLTF第三章的作业有一题是处理kaggle上的Titanic数据集，于是我又注册了一个帐号。它最近加入了一个比较友好的教程，照做就可以完成一次最基本的提交，我跟着昨晚一遍之后总算弄懂了几个问题，比如怎么看问题的数据在哪里，怎么提交。

[教程地址](https://www.kaggle.com/dansbecker/how-models-work)

有两个比较重要的坑：

一、教程中的数据集和实际需要使用的数据集不是同一个（大概是要防止照抄）。

二、由于kaggle改过界面，最后提交部分和教程描述的不一样。要退出notebook，到output标签点击提交文件才可以。

由于只是照着教程敲了一遍，模型的排名很低。明天看看能不能改进一下。



4.21 更新: 今天我阅读教程第二部分后自己调的模型反而比原来表现更差, 可能是因为盲目套用了方法, 没有仔细分析数据. 阅读了一些表现特别好的kernel和网上找到[文章](https://zhuanlan.zhihu.com/p/27424282), 做些笔记.

pandas: describe(): 查看均值, 方差等基本信息; cov(), corr(): 协方差, 相关系数

sklearn的plot_partial_dependence()绘制部分依赖图, 查看各个特征的重要性大小.

丢失特征: 用Imputer填充

类别类变量: 转为One-Hot编码

特征提取: 对于数值型变量, 可尝试线性组合/多项式组合. (防止数据泄露, 避免使用实际预测时无法提前得到的特征.)

特征选择: 特征与结果相关性越高, 特征越重要. 特征之间的相关性很高, 说明可能存在冗余特征. 可以对于特征的选择建模优化.

比起直接划分, 交叉验证可显著提高模型表现.

常见的Ensemble方法: Bagging, Boosting, Stacking, Blending.

常用工具:

Numpy | 必用的科学计算基础包，底层由C实现，计算速度快。
Pandas | 提供了高性能、易用的数据结构及数据分析工具。
NLTK | 自然语言工具包，集成了很多自然语言相关的算法和资源。
Stanford CoreNLP | Stanford的自然语言工具包，可以通过NLTK调用。
Gensim | 主题模型工具包，可用于训练词向量，读取预训练好的词向量。
scikit-learn | 机器学习Python包 ，包含了大部分的机器学习算法。
XGBoost／LightGBM | Gradient Boosting 算法的两种实现框架。
PyTorch／TensorFlow／Keras | 常用的深度学习框架。
StackNet  |  准备好特征之后，可以直接使用的Stacking工具包。
Hyperopt  | 通用的优化框架，可用于调参




