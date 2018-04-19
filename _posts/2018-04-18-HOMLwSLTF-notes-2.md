---
title:  "HOMLwSLaTF第二章阅读笔记"
excerpt: "第二章 End-to End Machine Learning Project"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  - 机器学习
---

这章通过一个例子(房子价格的数据集)让读者了解机器学习的基本流程.

## 常用数据仓库

数据仓库: UC Irvine Machine Learning Repository, Kaggle datasets, Amazon's AWS datasets

目录: Open Data Monitor, Quandl, List of Machine Learning Datasets(Wikipedia), Quora, /r/datasets

## 问题描述

数据管道: 由一系列数据处理组件有序组成. 组件之间存在IO关系, 一般异步工作.

1. 确定学习的目的, 即当前数据组件的输出. 这个例子中学习的目的为预测一个区域的房价中位数.

2. 选择学习类型. (监督/无监督/半监督; 分类/回归; 在线/离线 等)

这个例子中, 问题的分类为: 监督/一元回归/离线学习.

3. 选择评估函数, 即评价学习效果和理想效果差距的指标. 这个例子中选择RMSE(均方误差根)函数.

例:RMSE(l_2 norm), MAE(l_1 norm), l_k norm

P38 Notations

## 获取资料

下载数据csv, 用pandas读取. DataFrame是pandas中一种表格型的数据结构. 可以用info()获取数据项基本信息, value_counts()查看数据出现的频率, describe()查看各特征基本统计数据.

可以用matplotlib进行简单的数据可视化. 例如show()可以展示绘制的图像, 如hist绘制的直方图.

从图形中可以获得一些数据的信息. 在本例中, 可以得知部分特征被设置了上下限, 超过的部分被舍去; 特征之间的量级差距较大; 部分特征严重偏向中位数的左侧或者右侧.

创建数据集. 在本例中, 随机抽取20%数据作为测试集, 剩余部分作为训练集. 

有时, 对于划分后的数据集会存在复用的需求. 使用numpy的random.seed()选择一个种子可以实现随机数选择状况的重现, 但是这个方法对于增量数据不具有泛用性. 一个更好的方法是规定一个hash策略, 根据数据项编号进行划分.

也可以用Scikit-Learn提供的train_test_split()快速划分测试集.

在较小的数据集上, 随机抽样的效果可能并不好, 得到的测试集缺少代表性. 这时可以考虑采用分层抽样作为替代. 分的层不宜过多, 可以通过统一缩放特征值/手动规定特征取值上下限减少层数. sklearn也提供StratifiedShuffleSplit的分层抽样方法. 

## 数据可视化

可尝试将各特征可视化. 在本例中可以建立经度, 纬度为x/y轴的散点图, 研究数据项的地理位置分布状况. 对于plot()方法, 通过调整alpha参数可以专注于密集部分的数据.  可以通过规定s, c参数让点的直径/颜色也代表其他特征.

发现相关性. 例如通过对数据矩阵调用corr()方法计算某特征与其他特征间的相关系数, 可直观查看各特征间的线性相关性大小. 此外, pandas的scatter_matrix()方法可以作出任意两组特征间的散点图.

此外,  一些具有整体/部分关系的特征之间的比例可能是更有代表性的特征.

## 数据预处理

最好将此步骤自动化. 

数据清理. 某些数据项可能有缺失的特征, 一般有三种处理方法: 删除特征; 删除数据项; 赋值(均值, 中位数, 0, etc.). 这一步可以用DataFrame的dropna(), drop(), fillna()等方法完成. 也可以使用sklearn的Imputer.

处理字符串/离散分类型特征. 可以用sklearn的LabelEncoder进行字符串到数值的映射. 此外, sklearn的OneHotEncoder可以把离散特征的某个取值映射到欧式空间中的某个点(输出为一个scipy稀疏矩阵, 可用toarray()转换为numpy矩阵), 防止离散特征被看作有序的.

自定义的特征映射. 对于一个自定义的映射类, 让它继承sklearn的BaseEstimator和 TransformerMixin类, 编写\_\_init\_\_()/fit()/tramsform()方法即可. 书中的例子实现了一个根据房间数, 人口数和住房数给出新特征的映射器.

特征值缩放. 主要有两种方法: min-max缩放(正则化), 标准化. (网上很多博文混淆缩放(scaling), 正则化(normalization), 标准化(standardization)这三个概念. 我在做笔记时不会照搬网上的通用译名, 若是存在偏差, 一切以原文为准.)

正则化: (当前特征值-特征最小值)/(特征最大值-特征最小值), 把特征值缩放到[0,1]的范围.

标准化: (当前特征值-特征均值)/特征方差. 相比正则化, 缺点为不能把特征值缩放到固定的范围内, 优点为受异常值影响较小.

处理的管道化. sklearn的Pipeline可以帮助确保数据处理的步骤顺序正确.  把各个函数封装到一个Pipeline中, 将原数据集传入其的fit_transform()即可.


## 选择和训练模型

sklearn自带一些模型的实现, 如LinearRegression(), DecisionTreeRegressor(), RandomForestRegressor(). 可以使用mean_squared_error计算预测值和实际值的均方误差.

交叉检验.  sklearn的cross_val_score对其进行了实现, 可通过参数规定模型, 评估函数, 划分数. (对于此函数, 调用效用函数的效果比损失函数更好, 因此在使用损失函数时可以取负)

最后可用display_scores()查看评估结果的均值和方差. 如果模型在训练集表现得显然更好, 说明存在过拟合.

应当对模型进行持久化, 以方便选择最佳的模型.

## 调整模型

网格搜索. 根据交叉检验结果, 暴力地尝试给定定义域内超参数的组合, 得到平均误差最小的模型. sklearn中的GridSearchCV对其进行了实现. 还可用best_estimator_.feature_importances_计算特征的重要程度.

随机搜索. 当搜索空间过大, 网格搜索因时间复杂度太大效果不好. 随机搜索的策略是随机生成超参数的取值. sklearn中的RandomizedSearchCV对其进行了实现.

集成方法. 将多个模型进行组合.

使用测试集进行预测. 由于此前已将数据处理管道化, 对测试集进行相同调用即可. 不要在测试集上调整超参数, 由于测试集更小, 在其上调参会导致过拟合更严重.

系统的维护. 编写定期训练模型的程序, 以保证模型在最新的数据集上仍有较好表现. 同时,要定时对结果进行分析.