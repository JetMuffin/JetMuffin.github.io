title: 文本挖掘初步 - 文本特征值提取
tags: [tfidf,卡方校验,文本特征值]
categories: 设计开发
date: 2015-06-26 22:37:00
---

## 背景

继续上文[文本中文分词](http://www.jetmuffin.com/archives/225)的内容，本文的目的是从文本中提取出其特征向量，以便之后的分类器进行分类。

## 语料库

本文采用Sougou实验室的语料库，下载地址为：[搜狗实验室文本分类资料](http://www.sogou.com/labs/dl/c.html)。其中预料文件大小不一，有mini版(136K)，精简版(30M)，以及完整版(108M)，这里笔者就直接上最大的语料了～:lol:

**文档结构：**

- C000007 汽车
- C000008 财经
- C000010  IT
- C000013 健康
- C000014 体育
- C000016 旅游
- C000020 教育
- C000022 招聘
- C000023 文学
- C000024 军事

**注意：**搜狗的语料库均为GBK编码，若在Linux环境下会出现乱码影响处理，建议修改文件编码为utf-8

## 词袋模型

利用中文分词工具，我们对每篇文章进行分词，去停用词后，用一个Map存储每个词的相关信息，保存形式为单词 <词的文本字符串, 词的相关属性>，其中单词(Term)的相关属性有(词性、tf、df、tfidf、chi等)。这样，遍历数据集目录和文件，就能去将全部的文档分词，最终就能构建词袋模型。

其中词(Term)的类如下：

```java
package com.jetmuffin.TextClassifier.common;

public class Term {
    private int id;
    private String word;
    private String lexicalCategory = "unknown";
    private int freq = 0;
    private double tf;
    private double idf;
    private double tfidf = 0;
    private double chi = 0;

    public void setId(int id){
        ....
    }
    ......
```

而词袋的存储Map格式如下:

```java
    private int totalDocCount;
    private final List labels = new ArrayList();
    // Map<类别, 文档数量>
    private final Map<String, Integer> labelledTotalDocCountMap = new HashMap<String, Integer>();
    // Map<类别, Map<文档 ,Map<词, 词信息>>>
    private final Map<String, Map<String, Map<String, Term>>> termTable = new HashMap<String, Map<String, Map<String, Term>>>();
    // Map<词 ,Map<类别, Set<文档>>>
    private final Map<String, Map<String, Set>> invertedTable = new HashMap<String, Map<String, Set>>();
```

## 特征值(TFIDF)计算

tfidf是文本分类中最常使用的特征权值，其中tf(Term Frequency)为词频，idf为(Inverse Document Frequency)，tf反映了该词在该类文本中的热门程度，而idf则反映了该词对于其他文本更好的区别能力。

tfidf的计算方式如下：

![tf](http://i4.tietuku.com/218e13597b4ea3d8.png)![](http://i4.tietuku.com/c20eec142f06b175.png)![](http://i4.tietuku.com/a15b04b7d23c4837.png)

## 卡方拟合检验

经上一步计算出的tfidf值作为文章的特征值，但因为文章所包含的词往往非常多，特征向量的维数巨大，可能会达到上千维之多。因此需要对文章的特征向量进行降维。

进行降维，可以达到以下效果：

*   避免过拟合（over fitting），提高分类准确度
*   通过降维，大大节省计算时间和空间
我们选择基于卡方统计量（chi-square statistic， CHI）技术来实现选择，这里根据计算公式：

![](http://i4.tietuku.com/2dce06a9ac95d718.jpg)

其中，公式中各个参数的含义，说明如下：

*   N：训练数据集文档总数
*   A：在一个类别中，包含某个词的文档的数量
*   B：在一个类别中，排除该类别，其他类别包含某个词的文档的数量
*   C：在一个类别中，不包含某个词的文档的数量
*   D：在一个类别中，不包含某个词也不在该类别中的文档的数量
使用卡方统计量，为每个类别下的每个词都进行计算得到一个CHI值，然后对这个类别下的所有的词基于CHI值进行排序，选择出最大的topN个词；最后将多个类别下选择的多组topN个词进行合并，得到最终的特征向量。

## 向量空间模型

提取文档的特征向量，目的是建立向量空间模型，每个文档 dj 可以用标引项向量来表示  (a1j,a2j,…,aMj)  ![](http://i4.tietuku.com/e665571f39dec3cb.png)

利用向量空间模型，可以很容易计算出两个文档的相似度：

*   余弦公式（计算向量夹角）
*   欧式距离（计算两点距离）
计算出了文本相似度（文本距离），则可以进行我们的下，即进行分类器的建立了。我们将在后续的文章继续研究不同的分类方法对文本的分类，包括：

*   KNN
*   SVM
*   Naive Bayes
*   BP Neural Network (furthermore, RBF Neural Network)