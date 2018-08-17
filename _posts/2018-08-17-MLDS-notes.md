---
title:  "MLDS slide笔记"
excerpt: "notes of Machine Learning and having it deep and structured"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Deep_Learning
tags:
  - 深度学习
---
## Why Deep Structure

$$
给定一个正误差\epsilon，对于一个L-Lipschitz函数f*, 这个函数可以由K个神经元构成的网络拟合，如果存在f属于N(K)使得max|f(x)-f*(x)|<=\epsilon (0<=x<=1)。因此只要神经元数量足够，浅层网络也能拟合任意函数。
$$

主要使用深层结构，是因为单层网络的一个神经元只能对应一段线性函数，但是如果网络的宽度是K，深度是H，那么整个网络可以对应K^H段线性函数。

当目标函数满足某种最低复杂度要求时，使用深层网络结构是优于浅层网络结构的，且优于的程度是指数级别的。

## Optimization

对一个神经网络来说至少存在指数级个数的全局最小值点，因为你将某一层的神经元重新排列后并不改变损失函数值。

深度学习中损失函数是非凸函数，因此对于它的优化是一个NP-Hard问题。

### 梯度为0的点

在梯度下降过程中，我们希望陷入局部极小点而不希望陷入鞍点。

在\theta^0点用泰勒级数展开f(\theta)，可知Hessian矩阵决定了f(x)的曲率。

如果在某点梯度为0，且Hessian矩阵正定，说明该点是一个局部极小点。

$$
H\ is\ positive\ definite\rightarrow x^THx>0\rightarrow Around\ \theta^0:f(\theta)>f(\theta^0\rightarrow local\ minima)
$$

可以用牛顿法解出梯度为0的点，但：
- H不可逆时，无法使用此种方法
- 当参数量很大时，计算Ｈ以及Ｈ的逆矩阵代价很大
- 梯度为０的点有可能是局部最小值点，有可能是鞍点，也有可能是局部最大值点，牛顿法对此并不区分

因此，牛顿法不适合用在深度学习的问题中。

隐藏层层数越多，原点处的error surface越平坦，初始化在原点附近的话，如果是在平区域内，就很难逃出鞍点。

当所有隐藏层的神经元个数都不小于输入层、输出层的神经元个数，所有的局部最小值点就都是全局最小值点。

对于非线性深度网络，在一些条件下可以找到全局最小值。

### error surface

几乎所有的局部最小值点和全局最小值点有相似的损失函数值，因此找到一个局部最小值点已经足够好了。

假设神经网络有N个参数，那么其对应的Hessian矩阵有N个特征值，假设每个特征值为正、为负的概率各为0.5。

当这个网络非常大时，很难遇到局部最小值点，鞍点才是我们需要担心的。（例：N=1时，1/2局部最小，1/2局部最大，几乎不可能取到鞍点；N=10时，1/1024局部最小，1/1024局部最大，几乎每一个临界点都是鞍点）

特征值取负的几率与当前的损失值有关，损失值越大，有越多的路线使得损失下降，特征值取负的可能性越大。因此，鞍点比较容易出现在损失值大的地方，而局部最小值比较容易出现在损失值小的地方。

## Generalization

用VC维度(d_vc)衡量一个模型的函数集的大小，如果两个模型的表现相同，应该优先选择VC维更小的那一个。

衡量泛化度的指标：sharpness, sensitivity(取值越小，泛化度越高)

对于网络f, y=f(x), Jacobian矩阵由y对x的偏导组成，x的sensitivity为Jcobian矩阵的Frobenius范数：
$$
\sqrt {\sum_i \sum_j (\frac{\partial y_j}{\partial x_i})^2}
$$
对于给定的正误差\epsilon, 某点\theta^0的sharpness为(\theta^0-epsilon, \theta^0+epsilon)内最大的损失函数值与损失函数在该点的取值之差，即L(\theta')-L(theta^0)。

## Computational Graph

blog.csdn.net/u013527419/article/details/70184690

## Special Network Structure

Sequence generation: based on RNN

Conditional sequence generation: RNN(encoder) + RNN(decoder) jointly train

Attention-based model: 给不同神经元分配不同的学习权重

训练技巧：一开始用标签当输入，模型稳定后用上一个神经元的输出当输入

Pointer network: 输出对输出序列的一系列指针

Recursive structure: 神经网络呈树形结构，recurrent structure是特殊情况（每层只有一个神经元）

Matrix-vector recursive network, Tree LSTM

Memory network: 根据查询计算attention, 用根据attention(权重)和文本向量得到的extracted information训练DNN

ReasoNet, Neural turing machine, Stack RNN

## Special Training Technology

### 训练策略: Batch normalization

feature scaling: 正则化特征，可以使梯度下降收敛更快
$$
x^r_i=\frac{x^r_i-m_i}{\sigma_i}(m_i=avg(dim(i)),\sigma_i=std(dim(i)))
$$

对于隐藏层，它们的值会在训练过程中不断改变，因此可以进行Batch normalization，把同一层的结点作为一个整体更新。

注意：不适用于小批量数据集

因为这样可能会破坏分布，设置两个待学习的变量\gamma, \beta还原分布。
$$
\hat x^r=\gamma^rx^r+\beta^r
$$
一开始没有batch, 实际不可能用整个训练集计算均值和方差，一般在训练时计算均值，方差的滚动平均值。

BN可以加快训练速度，缓解梯度爆炸/消失现象。

Layer Normalization, Instance Normalization, Weight Normalization, Spectrum Normalization

### 激活函数: SELU

- 可以取到正值和复制
- Saturation region
- 斜率大于1

### 网络结构: Highway Network

www.cnblogs.com/jie-dcai/p/5803220.html

Residual Network, Grid LSTM

### 自动决定超参数

https://cloud.google.com/blog/products/gcp/hyperparameter-tuning-cloud-machine-learning-engine-using-bayesian-optimization

## GAN(Generative Adversatial Network)

### 基本思想

Generator：一个神经网络或者函数，输入一个向量，输出一个维度更高的向量（生成结果）

Discriminator：一个神经网络或者函数，输入一个向量，输出一个数值（判别结果）

过程：

1. 固定G，更新D（学习给真实对象打高分，生成对象打低分）
2. 固定D，更新G（学习产生能得高分的对象）

即：
- 从数据中取样m个样本
- 从一个分布中取样m个噪音，将它们输入生成器，产生m个生成对象
- 梯度下降法更新判别器的参数以最大化\hat V(等价于最小化一个二元分类器的交叉熵)，循环k次
$$
\hat V=\frac{1}{m}\sum^m_{i=1}logD(x^i)+\frac{1}{m}\sum^m_{i=1}log(1-D(\hat x^i))
$$
- 另一个分布中取样m个噪音z^1...z^m
- 梯度下降法更新产生器的参数以最小化\hat V
$$
\hat V=\frac{1}{m}\sum^m_{i=1}log(1-D(G(x^i)))
$$

结构化学习：输出是一个数据结构，因为很多类别可能不存在训练数据，所以需要人工生成（生成器）。机器需要有大局观，需要有一个深层结构来捕捉元件之间的联系，所以生成器不能脱离判别器；不以元件组合的方式生成负样本，时间复杂度太大，所以判别器不能脱离生成器。

Auto-encoder：自监督算法，Generetor由两部分组成：Encoder把输入数据降维，Decoder接收Encoder的输出，重构数据，目标是与原始数据越接近越好

Variational Auto-encoder(VAE): Encoder产生的输出附加了噪声 (来自一个正态分布，其方差由Encoder学习得到)

![https://i.loli.net/2018/08/16/5b74d682c928c.png](https://i.loli.net/2018/08/16/5b74d682c928c.png)
![https://i.loli.net/2018/08/16/5b74d8f78ddf5.png](https://i.loli.net/2018/08/16/5b74d8f78ddf5.png)

Conditional GAN(CGAN): 输入不是随机向量而包含了环境，打分时要同时考虑输出是否真实+输出是否与环境符合

Unsupervised CGAN: 输入和输出的域不同（例：图像的风格转换），要考虑输出是否属于目标域

Disco GAN, Dual GAN, Cycle GAN, StarGAN

### 原理

Generator: ![https://i.loli.net/2018/08/16/5b74e0f0cb8ce.png](https://i.loli.net/2018/08/16/5b74e0f0cb8ce.png)

Discriminator: 

![https://i.loli.net/2018/08/16/5b74e43832166.png](https://i.loli.net/2018/08/16/5b74e43832166.png)
![https://i.loli.net/2018/08/16/5b74e438306d5.png](https://i.loli.net/2018/08/16/5b74e438306d5.png)
![https://i.loli.net/2018/08/16/5b74e4381eaa1.png](https://i.loli.net/2018/08/16/5b74e4381eaa1.png)
![https://i.loli.net/2018/08/16/5b74e437ede07.png](https://i.loli.net/2018/08/16/5b74e437ede07.png)

$$
G*=arg \min_G L(G)=arg \min_G \max_D V(G,D)\\
V(G_0,D_0^*)=JS\ div\ of\ P_data(x),P_{G_0}(x))
$$

### 总体框架

f-散度：衡量两个分布的不同程度，如果它们相同，则D_f(P||Q)=0. f是凸函数;f(1)=0
$$
D_f(P||Q)=\int_x q(x)f(\frac{p(x)}{q(x)})dx
$$

f(x)=xlogx: KL

f(x)=-logx: Reverse KL

f(x)=(x-1)^2: Chi Square

Fenchel Conjugate:

所有凸函数都存在共轭函数f*
$$
f^*(t)=\max_{x \in dom(f)}\{xt-f(x)\}
$$

![https://i.loli.net/2018/08/16/5b74ed2f91847.png](https://i.loli.net/2018/08/16/5b74ed2f91847.png)
![https://i.loli.net/2018/08/16/5b74ed2f8fcc7.png](https://i.loli.net/2018/08/16/5b74ed2f8fcc7.png)

### WGAN, EBGAN

大部分情况下，P_G,P_data不重叠
- 二者都是高维空间中的低维流形
- 取样数量需要充足

JS散度的缺点：当两个分布不重叠，JS散度永远是log2

Wasserstein GAN(WGAN): 使用wasserstein distance(earth mover's distance)衡量两个分布之间的距离

![https://i.loli.net/2018/08/16/5b74f1c5d09f6.png](https://i.loli.net/2018/08/16/5b74f1c5d09f6.png)

$$
V(G,D)=\max_{D \in 1-Lipschitz}\{E_{x~P_data}[D(x)]-E_{x~P_G}[D(x)]\}
$$

Improved WGAN(WGAN-GP)

![https://i.loli.net/2018/08/16/5b751def18a89.png](https://i.loli.net/2018/08/16/5b751def18a89.png)
![https://i.loli.net/2018/08/16/5b751def0832f.png](https://i.loli.net/2018/08/16/5b751def0832f.png)
![https://i.loli.net/2018/08/16/5b751def062bc.png](https://i.loli.net/2018/08/16/5b751def062bc.png)

Spectral Normalization: 使梯度范数小于1

Energy-based GAN (EBGAN)

使用auto-encoder作为判别器，好处：auto-encoder可以不需要generator辅助，直接用样本去预训练

### InfoGAN, VAE-GAN, BiGAN

InfoGAN: 输入包含一个类别码c，generator产生的输出还会送入一个classifier，让它预测输入的类别码，classifier和discriminator共享参数，只有最后一层不同

VAE-GAN：VAE结合GAN，GAN作为VAE的discriminator

BiGAN：

encoder接受输入x，输出z

decoder接受上一轮的z'，输出\hat x

discriminator接受z和\hat x, 判断输入是来自encoder还是decoder

Triple GAN, domain-adversarial training

### Application to Sequence Generation


可以用GAN优化seq2seq模型

#### RL(human feedback)

最大化期望

对于机器的输入c和输出x，用奖励函数R(c,x)打分
$$
\theta^*=arg \max_\theta \bar R_\theta\\
\bar R_\theta=\sum_h P(h) \sum(x) R(h,x) P_\theta(x|h) \approx \frac{1}{N}\sum^N_{i=1}R(h^i,x^i)
$$

Policy Gradient

$$
\theta^{t+1}=\theta^t+\eta \nabla \bar R_{\theta^t}\\
\nabla \bar R_{\theta^t}=\frac{1}{N}\sum^N_{i=1}R(c^i,x^i)\nabla log P_{\theta^t}(x^i|c^i)
$$

![https://i.loli.net/2018/08/16/5b7531993758c.png](https://i.loli.net/2018/08/16/5b7531993758c.png)

#### GAN(discriminator feedback)

从训练集中取得输入c和响应x，再从训练集中取得输入c'，用生成器得到\hat x=G(c')，训练判别器D，使得D(c,x)最大，D(c',\hat x)最小

因为判别器接收两个取样的输入，不能用梯度下降法，三种方法：Gumbel-softmax, Continuous input for discriminator, "Reinforcement Learning"(replace reward with discriminator)

RankGAN

### Evaluation of GAN

用最大似然函数估计，可能出现高质量低似然/低质量高似然的现象

奖励高方差结果，惩罚低方差结果

inception score = negative entropy of P(y|x) - entropy of P(y)
$$
=\sum_x \sum_y P(y|x)logP(y|x)-\sum_y P(y)logP(y)
$$

## Deep Reinforcement Learning

### Proximal Policy Optimization(PPO)

从环境给定的观测s_1开始，执行动作a_1, 根据奖励函数得到奖励r_1，a_1,s_1输入环境得到下一个观测s_2, 如此反复，根据奖励调整参数

$$
R(\tau)=\sum^T_{t=1}r_t,\bar R_\theta=\sum_\tau R(\tau)\rho_\theta(\tau)\\
\nabla f(x)=f(x)\nabla logf(x)\rightarrow \nabla \bar R_\theta=\sum_\tau R(\tau)\rho_\theta(\tau)\nabla log\rho_\theta(\tau)\\
\approx \frac{1}{N}\sum^N_{n=1} R(\tau^n)\nabla log\rho_\theta(\tau^n)=\frac{1}{N}\sum^N_{n=1}\sum^{T_n}_{t=1} R(\tau^n)\nabla log\rho_\theta(a^n_t|s^n_t)
$$
$$
输入policy \pi_\theta, 执行得到一系列\tau=(s,a),R(\tau); 梯度下降法\theta=\theta+\eta \nabla \bar R_\theta更新参数
$$

tip:

1. 给R(\tau^n)减去一个偏置量b, b \approx E[R(\tau)]
2. assign suitable credit (add discount factor)

importance sampling:

x^i is sampled from p(x), only have x^i sampled from q(x)

$$
E_{x\sim p}[f(x)]=\int f(x)p(x)dx=E_{x\sim q}[f(x)\frac{p(x)}{q(x)}]
$$

p(x)/q(x)=importance weight

PPO/PPO2/TRPO

![https://i.loli.net/2018/08/17/5b7627158bbef.png](https://i.loli.net/2018/08/17/5b7627158bbef.png)

### Q-Learning

state value function V^pi(s): 对于主体\pi, 到达状态s后期望的累计奖励，用Monte-Carlo或者Temporal-difference法进行估计

MC: 方差更大

TD: 方差更小，可能不准确

state-action value function Q^\pi(s,a): 对于主体\pi, 在状态s执行行动a后期望的累计奖励

Q-learning: 

- \pi和环境交互
- （通过TD/MC）学习Q
- 找到一个更好的\pi'(argmax_a Q)，重复此过程

### Actor-critic

### Sparse Reward

### Imitation Learning