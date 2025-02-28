# 新冠疫情相似句对判定大赛 top6方案
**比赛链接：**

*https://tianchi.aliyun.com/competition/entrance/231776/introduction*

**Team：**

ZJU小飞机

## 赛题分析

### 赛题背景

面对疫情抗击，疫情知识问答应用得到普遍推广。如何通过自然语言技术将问答进行相似分类仍然是一个有价值的问题。如识别患者相似问题，有利于理解患者真正诉求，帮助快速匹配准确答案，提升患者获得感；归纳医生相似答案，有助于分析答案规范性，保证疫情期间问诊规范性，避免误诊。

### 任务目标

比赛整理近万条真实语境下疫情相关的患者提问句对，要求选手通过自然语言处理技术识别相似的患者问题。

### 数据示例

|          query1          |             query2             | Label |
| :----------------------: | :----------------------------: | :---: |
| 剧烈运动后咯血,是怎么了? |   剧烈运动后咯血是什么原因？   |   1   |
| 剧烈运动后咯血,是怎么了? |    剧烈运动后为什么会咯血？    |   1   |
| 剧烈运动后咯血,是怎么了? | 剧烈运动后咯血，应该怎么处理？ |   0   |
| 剧烈运动后咯血,是怎么了? |  剧烈运动后咯血，需要就医吗？  |   0   |
| 剧烈运动后咯血,是怎么了? |  剧烈运动后咯血，是否很严重？  |   0   |

## 解决方案

### 数据划分

随机保留800条样本作为dev，剩余部分加入训练。

通过**相似传递性**进行数据增强，额外获得了约5000条样本。

### 模型融合

尝试了孪生网络、ESIM、sentence-bert、TextCNN、TextRNN、Bert-finetune等多种方法，其中后三种效果最好，加入融合。

### 提分trick

- 预训练模型权重选择上，[Roberta_wwm_large](https://github.com/ymcui/Chinese-BERT-wwm)、[UER]([https://github.com/dbiir/UER-py)线下实验效果最好
- 数据增强（相似传递）
- [对抗训练](https://zhuanlan.zhihu.com/p/91269728)（提升模型鲁棒性）
- 标签平滑（防止过拟合）
- 加权融合（自定义**Search**函数寻找最优融合权重）
- 多学习率设置（区分Bert、后接网络学习率）

### 最终结果

采用了3种模型共8个权重融合，线上测试集：**96.26**

## 总结

文本相似匹配属于NLP基础任务之一，相对简单，适合入门。

本次比赛的数据集干净且规模小，普通的BERT微调就能获得94-95%的准确率，完全可以在实际中落地应用。

深度学习模型对资源要求较高（如GPU），在实际应用中还需寻找**高准确率、低资源、毫秒级响应**的解决方案。