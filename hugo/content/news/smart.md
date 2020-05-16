---
title: "State-of-the-art Results on GLUE Benchmark"
date: 2019-12-05
draft: false
tags: [nlp,smart,bert,fine-tuning,GLEU]
Description: "SMART model achieves 89.9 on GLEU, even better than T5!"
---

> Our recent collabrative work with Microsoft Dynamics 365 AI and Microsoft Research AI ([paper](https://arxiv.org/pdf/1911.03437.pdf), [code](https://github.com/namisan/mt-dnn)) achieves state-of-the-art results in 5 of 9 [GLUE benchmark tasks](https://github.com/namisan/mt-dnn) and an overall GLUE task performance **89.9**, which outperforms all existing models.

The reuslts are summerized as follows:

| Method | CoLA | SST | MRPC | STS-B | QQP | MNLI-m/mm | QNLI | RTE | WNLI | AX | Score | #params| 
| ------ | ---- | --- | ---- | ----- | --- | --------- | ---- | --- | ---- | --- | --- | ---- |
| Previous SOTA | 70.8 | 97.1 | 91.9/89.2 | 92.5/92.1 | 74.6/90.4 | 92.0/91.7 | 96.7 | 92.5 | 93.2 | 53.1 | 89.7 | 11,000M |
| MT-DNN-SMART | 69.5 | 97.5 | 93.7/91.6 | 92.9/92.5 | 73.9/90.2 | 91.0/90.8 | 99.2 | 89.7 | 94.5 | 50.2 | 89.9 | 356M |

- *Previous SOTA*: T5

### **Reference**

- {{<paper
title="SMART: Robust and Efficient Fine-Tuning for Pre-trained Natural Language Models through Principled Regularized Optimization"
year="2020"
author="Haoming Jiang, Pengcheng He, Weizhu Chen, Xiaodong Liu, Jianfeng Gao and Tuo Zhao"
proce="Annual Conference of the Association for Computational Linguistics (ACL)"
arxiv="https://arxiv.org/pdf/1911.03437.pdf"
code="https://github.com/namisan/mt-dnn"
>}}