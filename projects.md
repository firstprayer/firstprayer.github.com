---
layout: page
permalink: /projects/
title: Projects list
modified: 5-18-2014
comments: true
---

You can refer to my <a href="/files/resume.pdf">resume</a> for a quick overview of select projects. Here is a full list of all projects.

**Multi-Task Recursive Neural Network** Sep. 2014 - Nov. 2014

Recursive neural networks (RNN) have attracted more and more attention for natural language processing (NLP) tasks. The advantage of RNN is its capability to capture the recursive structure of nature languages, as well as its effectiveness of learning word embedding features. However, current research of RNN mainly focuses on performance of single task. It is still unclear whether datasets containing different labels can help enrich word feature representation and jointly boost classification performance for different tasks.

In this project a multi-task framework to bridge different NLP tasks using RNN was developed. Focusing on sentiment classification and part-of-speech tagging, we investigated whether RNN can learn features that encode semantic and syntactic information of words.

We also handled difficult challenges when training multi-task RNN containing huge number of parameters. We evaluated our method using the Stanford Sentiment Treebank and the Penn Treebank.

**Twitter Data Analysis** Sep. 2014 - Nov. 2014

In this project, we took a massive amount of twitter data(over 1TB) and transformed it into desired format with Hadoop. We further implemented analytical web service that allow fast queries of different information. 

My work included but not limited to:

1.  Implemented <i>Extract-Transform-Load</i> of twitter data with AWS Streaming Map Reduce. 
2.  Configured MySQL/Hbase to store and index needed data
3.  Implemented and optimized web service with <i>Undertow</i> framework in Java

**Non-negative Matrix Factorization based Transfer Learning** Feb. 2014 – Jun. 2014

This research project aimed exploring the potential of <i>Non-negative Matrix Factorization</i> when used in Transfer Learning. Through this project we proved NMF can be used to efficiently learn a common feature space between different domains.

1.  Designed and implemented a program that can automatically download news documents with categories through RSS from major news websites
2.  Developed a program to automatically extract information from heterogeneous Chinese news webpages
3.  Designed and implemented a new transfer learning algorithm based on NMF. The algorithm achieves better results in different transfer learning datasets (including a text dataset collected as described above) than state-of-art common subspace learning methods

**[MonSQL](https://github.com/firstprayer/monsql.git)** 

This is a MySQL wrapper in Python, which aims at providing a similar interface as MongoDB for MySQL. 



