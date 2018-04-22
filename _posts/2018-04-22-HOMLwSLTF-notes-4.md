---
title:  "HOMLwSLaTF第四章阅读笔记"
excerpt: "第四章 Training Models"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  - 机器学习
---

## 线性回归

假设预测目标和特征线性相关. sklearn中的实现为LinearRegression.

线性回归模型: 

$$\hat y=\theta^T \mathbf x, \hat y为预测目标, \theta为参数向量, \mathbf x为特征向量(已加入常数项对应的特征)$$ 

代价函数: RMSE, Root Mean Square Error (为简化, 一般直接求MSE)

$$MSE(\mathbf X, h_\theta)=\frac{1}{m}\Sigma_{i=1}^{m}{\Big(\theta^T \mathbf x^{(i)}-y^{(i)})^2}$$

正则化方程: 

$$\hat \theta = (\mathbf X^T \mathbf X)^{-1}\mathbf X^T \mathbf y, \hat \theta为使RMSE最小的\theta取值, \mathbf y为预测向量$$

$$这个公式通过在 \mathbf X \theta=\mathbf y两边同时乘以\mathbf X^T解出.$$

设特征个数为$$n​$$, 特征矩阵求逆的时间复杂度约为$$O(n^3)​$$. 样本个数为$$m$$, 预测的时间复杂度是$$O(m)​$$.


## 梯度下降

模型目的为求代价函数的最小数值解. 每次迭代以局部梯度作为下一步方向. 步长过小时学习速度过慢, 步长过大时无法收敛到最小值, 停止过早时容易收敛到局部最小值.

由于MSE是凸函数, 可知无局部最小值.

使用梯度下降法前, 需对特征的取值范围进行缩放, 控制在一个统一的不过大的范围内, 否则训练时间会过长.

### 批梯度下降(BGD)

对于第j个特征, MSE的偏导数: $$\frac{\partial}{\partial \theta_j}MSE(\theta)=\frac{2}{m}\sum_{i=1}^{m}{\Big(\theta^T \mathbf x^{(i)}-y^{(i)})x_j^{(i)}}$$

故MSE的梯度为: $$\nabla_\theta MSE(\theta)=\frac{2}{m}\mathbf X^T (\mathbf X \theta - \mathbf y)$$

也就是说,批梯度下降在每轮迭代时都要遍历整个特征矩阵.

$$\theta_{next} = \theta + \eta \nabla_\theta MSE(\theta), \eta为步长(学习速率)$$

可以通过网格搜索(见第2章)选择一个合适的学习速率.

收敛速率:$$O(\frac{1}{iterations})$$

### 随机梯度下降(SGD)

更新$$\theta$$时, 用特征偏导数代替梯度, 每轮迭代不重新遍历. 优点是在n/m较大时也能有较高的速度, 缺点是准确性更差.

一个提高准确性的方法是: 开始时设一个较大的学习速率, 然后逐步缩小它.

在sklearn中的实现是SGDRegressor.

### 小批量梯度下降

介于BGD和SGD下降之间的一种算法. 每轮迭代, 随机选择一小组数据项计算梯度, 并据此对$$\theta$$进行更新. 准确性强于SGD, 速度稍低, 但在大数据集也有不错表现.

## 多项式回归

假设预测目标可以由一组特征组成的多项式来拟合, 先确定多项式的最高项次数, 再求解对应的参数. 相当于在原特征的基础上添加一些多项式特征, 然后当做线性回归问题求解.

sklearn中的PolynomialFeatures可以帮助生成多项式特征, 之后就可以用LinearRegression求回归方程.

多项式总特征数: $$\frac{(n+d)!}{d!n!}$$, 其中n为原特征数, d为最高项次数.

## 学习曲线

多项式最高项次数过高时模型会过拟合. 学习曲线可以帮助判断模型是否过拟合.

通过画出不同训练集大小时训练集和测试集的误差大小, 可以判断模型的拟合状况. 本例中根据RMSE判断模型表现好坏.

欠拟合: 增大训练集时, 训练集和测试集的预测误差率均逐渐稳定在一个较高的程度.

过拟合: 一开始训练集几乎没有误差, 测试集的预测误差随着训练集增大逐渐降低, 但训练集大小到达某个程度时测试集误差会突然上升到一个很高的值.

模型在实际预测时产生的误差是泛化误差, 泛化误差在不同方面的表现, 说明模型存在不同的问题.

高偏差: 很可能是因为欠拟合

高方差: 很可能是因为过拟合

不可约误差(无法消除的误差): 很可能是因为噪音, 说明数据需要进一步清洗

## 正规化线性模型

在正规化前需要对数据进行缩放, 因为正规化模型对特征的尺度敏感.

### 岭回归(L2正规化)

正规化的线性回归, 添加了一个正规化超参数. 在拟合数据的基础上, 要求模型的参数越小越好, 从而避免过拟合. 在sklearn中的实现是Ridge.

$$J(\theta)=MSE(\theta)+\alpha\frac{1}{2}\sum^n_{i=1}\theta_i^2, 注意偏差项\theta_0未被正规化$$

$$\hat \theta = (\mathbf X^T \mathbf X+\alpha \mathbf A)^{-1}\mathbf X^T \mathbf y$$

正规化参数$$\alpha$$也就是权重向量的L2范数(欧几里德距离). $$\alpha$$越大, 模型预测时的方差越小, 偏差越大. 当$$\alpha=0$$, 模型退化为普通线性回归模型.

### Lasso回归(L1正规化)

类似岭回归, 不过选择的正规化参数是权重向量的L1范数(曼哈顿距离). 在sklearn中的实现是Lasso.

$$J(\theta)=MSE(\theta)+\alpha\frac{1}{2}\sum^n_{i=1}|\theta_i|$$

Lasso回归的特征是: 倾向消去不重要特征的权重, 甚至设为0. 也就是说, 它会进行特征选择, 输出一个稀疏模型. 在用一个进行了L1正规化的代价函数运行梯度下降法时, 如果有$$\theta_i$$被设为0, 可以用次梯度向量代替它.

### Elastic Net

介于岭回归和Lasso回归之间.

$$J(\theta)=MSE(\theta)+r\alpha\sum^n_{i=1}|\theta_i|+\alpha\frac{1-r}{2}\sum^n_{i=1}\theta_i^2$$

最好用正规化线性模型取代普通线性模型. 默认选择是岭回归, 如果觉得重要特征很少, 可以用Lasso或者Elastic Net. Elastic Net比Lasso更稳定.

### 早停止

防止因训练迭代次数过大而产生的过拟合, 在迭代次数到达一定时停止. 当前测试误差和最小误差之间的差到达一定值时, 早停止算法认为模型已经经过极小值点, 停止训练.

## Logistic回归

用于二分类的回归函数, 它在sklearn中的实现是LogisticRegression.

### 估计概率

$$\hat p=h_\theta(x)=\sigma(\theta^T\mathbf x), \sigma为sigmoid函数, \sigma(t)=\frac{1}{1+exp(-t)}$$

预测时:$$\hat p<0.5 则\hat y=0, \hat p\ge0.5 则\hat y=1$$

### 训练和误差函数

对于一项数据, 我们希望把正例预测为0, 负例预测为1的代价尽可能大:

$$y=1 则c(\theta)=-log(\hat p), y=0 则c(\theta)=-log(1-\hat p)$$

Logistic回归的损失函数:

$$J(\theta)=-\frac{1}{m}\sum^m_{i=1}[y^{(i)}log(\hat p^{(i)}+(1-y^{(i)})log(1-p^{(i)}))]$$

这个函数没有解析解. 但它是凸函数, 因此可以用梯度下降法来求得数值解.

它的偏导数为:

$$\frac{\partial}{\partial \theta_j}J(\theta)=-\frac{1}{m}\sum^m_{i=1}(\sigma(\theta^T\mathbf x^{(i)})-y^{(i)})x_j^{(i)}$$

### 决策边界

将正负例分离开来的一个假象边界. 可以通过绘制特征取值与正负例分类概率的函数, 寻找决策边界. 能100%分离开正负例的边界并非总是存在.

### Softmax回归

Logistic回归在多分类问题上的推广. 设置LogisticRegression的multi_class参数即可.

对于每一个数据项, Softmax会为每一个分类k计算一个分数$$s_k(x)$$, 它代表这个数据项被分到那个类的概率, 最后把这些分数输入到Softmax函数中得到最终分类.

Softmax分数:

$$s_k(x)=\theta_k^T\mathbf x$$

Softmax函数:

$$\hat p_k=\sigma(s(x))_k=\frac{exp(s_k(x))}{\Sigma^K_{j=1}exp(s_j(x))}$$

预测:

$$\hat y =\mathop{argmax}_k \sigma(s(x))_k=\mathop{argmax}_k s_k(x)=\mathop{argmax}_k(\theta_k^T \mathbf x)$$

使用的代价函数, 交叉熵函数, 它可以对错误的分类进行惩罚, 评估多分类的准确度:

$$J(\Theta)=-\frac{1}{m}\sum^m_{i=1}\sum^K_{k=1}y_k^{(i)}log(\hat p_k^{(i)})$$

对于第k个分类, 交叉熵函数的梯度向量为:

$$\nabla_{\theta_k}J(\Theta)=\frac{1}{m}\sum^m_{i=1}(\hat p_k^{(i)}-y_k^{(i)})\mathbf x^{(i)}$$

