---
title:  "HOMLwSLaTF第五章阅读笔记"
excerpt: "第五章 Support Vector Machine"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Machine_Learning
tags:
  - 机器学习
  - SVM
---

本章主要介绍SVM(Support Vector Machine, 支持向量机).

## 线性SVM分类

对于线性可分的两个类别, SVM模型找出一条将它们分开的直线. SVM的优化目的是: 在保证将这两个类别里的数据项分开的同时, 让分别在两个类别中离这条线最近的数据项之间的距离最小化. 可知SVM所学习的决策边界实际上完全由两个离其最近的数据项决定, 它们被称为支持向量.

### 软边界分类

硬边界分类意味着决策边界必须将两个类别中的所有数据项完全分开, 不得出现误判. 这在实际数据集中常常是难以达成的(考虑线性不可分数据/离群点), 因此有软边界分类, 即只要误分类点和决策边界之间的距离不超过一定值即可.

## 非线性SVM分类

### 多项式Kernel

### 增加相似度特征

### 高斯RBF Kernel

### 计算复杂度

## SVM回归

## SVM详解

### 决策函数和预测

### 训练目标

### 二次规划

### Kernelized SVM

### 在线SVM

