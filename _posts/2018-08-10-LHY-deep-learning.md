---
title:  "李宏毅深度学习slide笔记"
excerpt: "note of Understanding Deep Learning in One Day"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Deep_Learning
tags:
  
---
## 优化技巧

损失函数：如果输出层使用softmax函数，cross entropy优于square error

ReLU的优势：

- 计算快
- 生物学上的原因
- 解决gradient vanishing问题

ReLU变种：Leaky ReLU, Parametric ReLU

ReLU是一种特殊的Maxout，Maxout由多个线性函数拼接而成，一次Maxout代表一段线性函数

学习率：每迭代几次，可更新一次，将其削减
$$
e.g.:\eta ^t=\frac{\eta}{\sqrt t+1}
$$

有多种方法：Adagrad, RMSprop, Adadelta, "No more pesky learning rates", AdaSecant, Adam, Nadam

如果Dropout率为p则测试时所有结点的权重乘以(1-p)

More out dropout: More reference for dropout(Srivastava, Baldi, Hinton), Maxout(Goodfellow), Dropconnect(Wan), Annealed dropout(Rennie), Standout(J.Ba)

## CNN

卷积层： 提取局部特征。一些特征很小，神经元不需要查看整个样本就能发现，所以可以靠检查每个小区域提取特征；一个特征可能在不同的样本中出现，两个检测的神经元可以使用同样的参数

stride: filter移动的步长

zero padding: 允许filter的一部分覆盖到样本外(空缺部分补0)

样本经filter卷积后形成一个feature map, 每个特征都共享filter所对应的参数，对于图像处理，一个卷积层中每个filter对应一个通道

池化层：降维。对样本重新取样不会改变样本的分类，对样本重新取样减小维度数可以加快学习效率

卷积会增加维度，所以卷积层后面会加池化层

全连接层：分类。将特征映射到样本标记空间

## RNN

双向RNN：增加反向的网络，正向/反向对于同一位置的输出一起决定该位置的最终输出

LSTM

x^t, h^t(hidden state), c^t(cell state), y^t

input gate, memory cell, forget get, output gate

input->cell(long-term)->hidden(short-term)->output
$$
(w^i, w^f, w^o), x^t, h^{t-1} -> (z^i, z^f, z^o)\\
z^f, z^i, z, c^{t-1} -> c^t\\
z^o, c^t -> h^t\\
h^t, W' -> y^t
$$

LSTM与gradient vanishing：只要forget gate不关闭，input就会和memory相加，不会出现gradient vanishing



GRU

基本结构与朴素RNN相同(x^t, h^t, y^t)
$$
w^r, x^t, h^{t-1} -> r\\
w^z, x^t, h^{t-1} -> z\\
h^{t-1}, r -> h^{t-1'}\\
h^{t-1'}, w, x^t -> h'\\
h', h^{t-1'} -> y^t, h^t
$$

Clockwise RNN, Structurally Constrained Recurrent Network(SCRN), Vanilla RNN initialized with identity matrix + ReLU activation

Connectionist Temporal Classification(CTC)

## 其他


### Auto-encoder

自监督学习，可实现数据降维

Deep Auto-encoder = NN encoder + NN decoder

reference: Reducing the dimensionality of data with neural networks, Contractive auto-encoders: Explicit invariance during feature extraction, Extracting and composing robust features with denoising autoencoders


### Word embedding

词汇映射到向量空间

count based: glove

prediction based: Continuous bag of word(CBOW), Skip-gram

同义词查找: Rome:Italy=Berlin:? = V(Berlin)-V(Rome)+V(Italy) (V is word embedding feature)

文档到向量:

Paragraph Vector: Distributed representations of Sentences and Documents

Seq2seq Auto-encoder: A hierarchical neural autoencoder for paragraphs and documents

Skip Thought: Skip-THought vectors

Others: Learning deep structured semantic models for web search using clickthrough data, A latent semantic model with convolutional-pooling structure for information retrieval, Recursive deep models for semantic compositionality over a sentiment treebank, Improved semantic representations from tree-structured long short-term memory networks

### Reinforcement learning

生成目标

Generative model

PixelRNN: Pixel Recurrent Neural Networks, Wavenet: A generative model for raw audio, Video pixel networks

Variation Auto-encoder(VAE):

Auto-Encoding Variational Bayes

Generating sentences from a continious space

GAN



