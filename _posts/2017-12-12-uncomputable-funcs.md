---
layout: post
title: "泛化和评估"
subtitle: "Generalization and Estimation"
author: "Mingxian Yang"
header-img: "img/post-bg-infinity.jpg"
header-mask: 0.3
mathjax: true
tags:
  - 机器学习
  - 计算理论
---

## 1. 泛化（	）

指的是模型依据训练时采用的数据，针对以前未见过的新数据做出正确预测的能力。

## 2. 过拟合与欠拟合

上次看了一篇 [链接名称](连接地址) 的博客

### 2.1 过拟合


### 2.2 如何控制过拟合
每个不同的数据集需要不同的程度的predictor灵活性。
- 取决于机器学习任务的复杂程度，还要针对现有数据的数量进行调整，例如：训练集很小的情况下，你的predictor可能就会需要简单一些才能降低过拟合的程度。
- 所以你可能需要一个‘控制旋钮💽’来调整predictor的灵活程度。
  
  大多数的算法都有这样的‘控制旋钮’：
  回归，
  朴素贝叶斯，
  决策树，
  kNN，
  SVM等

### 2.3 Training Error 与 Generalization Error 
- Training Error： 
  
    $$E_{t r a i n}=\frac{1}{n} \sum_{i=1}^{n} \operatorname{error}\left(f_{D}\left(\mathbf{x}_{i}\right), y_{i}\right)$$

    n是训练样本, 
    $$f_{D}\left(\mathbf{x}_{i}\right)$$ 
    是预测的值, 
    $$y_{i}$$ 
    是真实值。`error()`函数用来衡量两个数据是否相等，相差多少。

- Generalization Error：
  
  \s这就是衡量我们对未来数据的处理
  \我们不知道
  $x_{i}$ ，不知道标签
  $y_{i}$ 。但是已知所有
  $\{x, y\}$的范围。
  下面是Generalization Error的计算公式。

  $$E_{g e n}=\int \operatorname{error}\left(f_{D}(\mathbf{x}), y\right) p(y, \mathbf{x}) d \mathbf{x}$$
  其中

通常情况下，有$E_{\text {train}} \leq E_{\text {gen}}$

最近实在太忙了。。。以后慢慢完善更新吧