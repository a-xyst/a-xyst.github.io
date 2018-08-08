---
title:  "情感分析"
excerpt: "class notes at SIAT"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  - 深度学习
  - NLP
---

# 背景

情感分析的构成：主体分析+目标分析+观点分析

观点可按：直接/比较、显式/隐式、粒度（文档级别、句子级别、特征级别）分类

观点质量分析：以顾客评论为例，通常是一个回归任务，使用各种特征（评论长度、评论得分、某些POS 标签的数量、观点词、TF-IDF权重得分、提到的产品特征和品牌、与其他产品的对比），主要应用为观点搜索

## 情感分析方法

无监督方法：基于词库的分析

词典方法基于语法联系进行分析（例：同义词、反义词），缺点是句子粒度级别的准确性不高，使用一个种子列表，并且扩展它

语料方法从有标签样本学习，依赖大规模语料中的语义或者规律，缺点是需要手工标注的数据

机器学习：朴素贝叶斯、最大熵分类器、SVM（假设独立特征成对地存在）

深度学习：Auto-encoder, CNN, LSTM（避免特征工程）

# 基于词库的情感分析

基本思想：根据句中观点词的倾向，决定句子的情感倾向

观点词=情感词=倾向词

主要步骤：
- 决定情感词词库
- 从训练样本中查找情感词和标签并计数
- 查找情感词前的程度词，根据其为情感词设置权重
- 查找情感词前的否定词，如果存在则把对应的权重设为相反数
- 计算句子的情感分数

细粒度情感词：anger, disgust, fear, joy, sadness, surprise等

常见情感词词典：
- 英文：SentiWordNet, General Inquirer, Opinion Lexicon, LIWC, MPQA
- 中文：HowNet, DUTIR, NTUSD

## 构建词典

### Bootstrapping

1. 手动收集一系列已知倾向的观点词
2. 从一个在线词典（例：WordNet）中查找它们的同义词和反义词，从而扩充单词集
3. 重复以上步骤直到无法扩充单词集，手动检查单词集的正确性，也可以根据WordNet添加额外信息

### PMI(pointwise mutual information)

数据来自epinions.com中关于车、银行、电影和旅行距离的评论

1. 进行POS(part of speech) 标签ging, 根据一些手动规定的规则，提取含有形容词和副词的词组

例：提取双词短语的POS 标签规则

| First word     | Second word         | Third word(Not Extracted) |
| -------------- | ------------------- | ------------------------- |
| JJ             | NN or NNS           | anything                  |
| RB, RBR or RBS | JJ                  | not NN nor NNS            |
| JJ             | JJ                  | not NN nor NNS            |
| NN or NNS      | JJ                  | not NN nor NNS            |
| RB, RBR or RBS | VB, VBD, VBN or VBG | anything                  |

2. 根据PMI度量，估计每个被提取词组的倾向

PMI即为观测一个词时，如果另一个词出现，得到的信息量

$$
PMI(word_1,word_2)=log_2 \frac{P(word_1,word_2)}{P(word_1)P(word_2)}\\
(P(word_1,word_2)=\frac{hits(word1\ NEAR\ word2)}{N^2})
$$

一个词语的观点倾向（OO）是基于它和正向参考词、负向参考词的关联计算得出的

例：polarity(phrase) = PMI(phrase,"excellent") - PMI(phrase,"poor")

Word2Vec

词库分析法的缺点：不能辨认在特定上下文中的观点词；观点词的数量有限，且在不同领域的作用可能不同

# 基于机器学习的情感分析

直接用监督学习方法对评价进行二分类，类似于文本分类

分类方法：朴素贝叶斯、最大熵分类器、SVM、逻辑回归

特征提取：否定标签、一元分词、二元分词、POS标签、位置、用户打分

## 数据预处理

分词：英文可根据空格/tab/换行分词

去除停用词：去掉区分度很低的词，优点可以减少数据的复杂度，缺点可能降低召回率、降低作者识别度。NLTK可以用来分词

否定词：弱情态词(bad, good)在否定语境下表相反的意思，强情态词(superb, terrible)较为复杂

否定词标记：把在否定词和下一个词之间的词全部标记为否定态，也可以进行其他类似的范围标记，如引用标记

## 特征提取

特征：单词、n-gram(n元分词)、词类、观点词、增强词移位词情态动词、语法依赖

特征选择：基于频率、信息增益、优势比、互信息

特征权重：基于单词的出现或者频率、IDF、单词位置

在平衡训练集上，SVM的分类准确性最强，使用特征为一元分词（独立单词）

# 基于深度学习的情感分析

深度学习模型包含：
- 单词向量化（例：word embedding）
- 神经网络结构
- - 时间递归神经网络(Recurrent Neural Network, RNN)
- - 结构递归神经网络(Recursive Neural Network)
- - 卷积神经网络(CNN)
-  Softmax层

## 单词向量化

传统方法：One Hot Encoding（一个特征代表一个单词）

word embedding：把每个单词保存为空间中的一个点，空间是靠基于大型语料库的无监督学习建立，空间维度固定（一般为300）

### word2vec

流行的建立低维度文本空间的软件包，包含：

-  两个独立模型：CBoW，Skip-Gram
-  各种训练方法：Negative Sampling, Hierarchical Softmax
-  丰富的预处理管道：Dynamic Context Windows, Subsampling, Deleting Rare Words

CBoW: 根据上下文预测当前词，训练速度较快，预测频繁词效果更好

Skip-Gram：根据给定的词预测它周围的词，通常表现更好，预测稀有词效果更好

维度数和窗口大小影响训练效果

## RNN

Paper: Recursive Deep Models for Semantic Compositionality Over a Sentiment Treebank

LSTM：

[https://github.com/shashankbhatt/Keras-LSTM-Sentiment-Classification]
[https://inclass.kaggle.com/c/si650winter11]

## CNN

Paper: Convolutional Neural Networks for Sentence Clssification

[https://github.com/cmasch/cnn-text-classification]

深度学习资源：Pylearn2, Theano, Caffe, Torch, Pytorch, Deeplearning4j, Word2vec, GloVe, Doc2vec, StanfordNLP, TensorFlow

# 其他情感分析方法

多方面的情感分析

IAN

Domain adaptation(transfer learning): 情感分类受领域影响，可使用SDA

半监督学习

co-training