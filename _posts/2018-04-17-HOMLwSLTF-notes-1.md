---
title:  "HOMLwSLaTF第一章阅读笔记"
excerpt: "听说这本书的质量很不错, 决定读一读. 由于只有英文版, 打算边读边做笔记. 书名:Hands-On Machine Learning with Scikit-Learn and TensorFlow"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  
---

机器学习: 研究数据, 选择模型, 用数据训练模型, 使用模型对测试数据进行预测(泛化).

## 机器学习的种类

### 监督/无监督学习

#### 监督学习

用已标注的训练集训练模型, 典型应用为分类和回归.

常见算法:K-近邻, 线性回归, Logistic回归, SVM(Support Vector Machine), 决策树和随机森林, 神经网络.

#### 无监督学习

用未标注的训练集训练模型, 典型应用为聚类, 可视化, 特征提取, 异常检测, 关联规则学习.

常见算法:

聚类:K-均值, HCA(Hierarchical Cluster Analysis), EM(Expectation Maxmization)
可视化和降维:PCA(Principal Component Analysis), Kernel PCA, LLE(Locally-Linear Embedding), t-SNE(t-distributed Stochastic Neighbor Embedding)
关联规则学习:Apriori, Eclat


#### 半监督学习

部分训练集有标注, 一般结合监督, 无监督算法.

#### 增强学习

描述这样一个过程:一个可观测环境的agent, 通过执行一些动作会得到相应的奖励/乘法, 它根据学习反馈来调节自己的行动规则.

### 在线/离线学习

#### 在线学习(增量学习)

训练好模型后要学习新训练数据的情况下, 不需要从头重新训练, 只需要学习新增部分, 因此可一批批输入数据进行模型的训练.

#### 离线学习(批学习)

模型为输入数据后一次性训练完成, 训练好模型后要学习新训练数据的情况下, 必须从头重新训练.

### 基于实例/基于模型的学习

基于实例的学习:对于新的数据, 分析它与旧数据之间存在的关联, 并据此产生输出(例:K-近邻).

基于模型的学习:建立一个模型, 用数据来训练模型的参数. 对于新的数据, 将其输入模型即可产生输出(例:线性回归).

## 机器学习中常见问题

训练数据量不足

训练数据无代表性

数据质量差

存在过多无关特征

训练数据过拟合

训练数据欠拟合

## 课后练习

1. Examine data, choose a proper model, train the model with training data, finally do predictions of testing data with the model.
2. Categorize, regression, clustering, association rule learning.
3. Training data contain desired solutions.
4. Categorize, regression.
5. Clustering, visualization, Feature extract, anomaly detection.
6. Reinforcement learning.
7. Clustering.
8. Supervised.
9. Learning can be done incrementally, when new data is added, there is no need to start over training.
10. A learning algorithm to trains systems on huge datasets that cannot fit in one machine's main memory.
11. Instance-based learning.
12. Hyperparameter can't be learned and is set when the model is chosen. The parameter of a model can be learned by studing training data.
13. They search for parameters of a model. They change the parameters towards the direction that makes less mistakes when predicting solutions of training set. They predict solutions of testing set according to the model and the learned parameters.
14. Overfitting, irrelevant features, nonrepresentative training data, insufficient quantity of training data.
15. Overfitting. Simplyfy the model by selecting one with fewer parameters/gather more training data/reduce the noise in the training data.
16. A data set that is not used when training the model. Testing sets are used to test the generalization ability of a model.
17. To minimize overfitting.
18. Overfitting happens.
19. Randomly shuffle the data set into some subsets with equal size, for every subset, choose it as testing set and train with other sets. After all training are done, choose the model that has the least average error rate. Cross validation can work well on a smaller dataset.