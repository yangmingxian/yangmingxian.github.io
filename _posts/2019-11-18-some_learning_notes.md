---
layout: post
title: "一些看到过的知识点总结"
subtitle: "待整理"
author: "Mingxian Yang"
header-img: "img/post-bg-infinity.jpg"
header-mask: 0.3
mathjax: true
tags:
  - 笔记
---

#### 1. PMI
逐点互信息PMI（Pointwise mutual information）：

[https://blog.csdn.net/baimafujinji/article/details/6509820](https://blog.csdn.net/baimafujinji/article/details/6509820)
#### 2. TF-IDF
TF-IDF(Term Frequency-Inverse Document Frequency, 词频-逆文件频率).
字词的重要性随着它在文件中出现的次数成正比增加，但同时会随着它在语料库中出现的频率成反比下降。

[http://www.tfidf.com/](http://www.tfidf.com/)

[https://blog.csdn.net/zrc199021/article/details/53728499](https://blog.csdn.net/zrc199021/article/details/53728499)

#### 3. 词向量Word2vec
NLP 里的词语，是人类的抽象总结，是符号形式的（比如中文、英文、拉丁文等等），所以需要把他们转换成数值形式，或者说——嵌入到一个数学空间里，这种嵌入方式，就叫词嵌入（word embedding)，而 Word2vec，就是词嵌入（ word embedding) 的一种。
[https://zhuanlan.zhihu.com/p/26306795](https://zhuanlan.zhihu.com/p/26306795)

#### 4. Neural network-inspired dense embeddings
Key idea: Each word in the vocabulary is associated with two vectors: a context vector and a target vector. We try to push these two types of vectors such that the target vector of a word is close to the context vectors of words with which it co-occurs.

#### 5.  Word2Vec 之 Skip-Gram
[https://zhuanlan.zhihu.com/p/27234078](https://zhuanlan.zhihu.com/p/27234078)

负采样的原理：

[https://zhuanlan.zhihu.com/p/39684349](https://zhuanlan.zhihu.com/p/39684349)

#### 6. 语义分析之一阶逻辑
[http://web.stanford.edu/~jurafsky/slp3/16.pdf](http://web.stanford.edu/~jurafsky/slp3/16.pdf)

[https://www.bilibili.com/read/cv3249938/](https://www.bilibili.com/read/cv3249938/)

[http://www.shuang0420.com/2017/04/07/NLP%20%E7%AC%94%E8%AE%B0%20-%20Meaning%20Representation%20Languages/](http://www.shuang0420.com/2017/04/07/NLP%20%E7%AC%94%E8%AE%B0%20-%20Meaning%20Representation%20Languages/)