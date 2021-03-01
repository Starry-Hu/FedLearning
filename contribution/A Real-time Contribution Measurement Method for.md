## 出处

A Real-time Contribution Measurement Method for
Participants in Federated Learning

arXiv:2009.03510v1 [cs.LG] 8 Sep 2020

`阅读时间：2021.2.28`目前读的第一篇贡献量论文

## 目的

- 介绍了一种**联邦学习的在线评价方法**，用于**对每个agent数据质量数量的评估**，并与博弈论的shapley值的效果进行比较

## 背景

- 随着联邦学习的发展，对于**参与联邦学习的每个agent，并没有一个好的评价标准**
- 本文提出了一种**联邦学习的在线评价方法**，并<u>与博弈论中的Shapley值结果进行了比较</u>，本文提出的方法<u>对数据的质量和数量更敏感</u>。

## 结论

- 提出了FedAtt的关注力联邦优化算法，并且基于关注力提出了计算各个客户端贡献量的算法
- 通过实验验证，本文提出的贡献量测量方法**能够合理公平地测量出各个客户端的贡献量**，并且相比shapely value<u>对于数据量大小和数据质量有更高的敏感性，评价更为准确</u>

## 主要内容

此处使用“客户端client”代替“代理agent”，在本文中意思一样

### 引言

1. 文中指出联邦学习的两种主要实现方法？

   > 解决不同客户端来帮助构建模型时的保护数据隐私问题

   - 通过各个客户端的子模型来构建中心模型，其中只传输模型参数
   - 使用加密技术来允许不同客户端之间的安全通信

2. 研究数据贡献的意义？

   - 为了促进联邦学习系统的工作有效性，需要<u>鼓励客户端共享数据、参与协作</u>，因此**信任分配和贡献奖励机制**十分重要

3. 本文提出的实现数据质量的公平测量方法？

   - 结合联邦学习的贡献激励机制
   - 模型聚合的注意力（关注）机制：用于决定所以客户端的贡献量
     - 计算联邦学习中每个客户的关注力权重，<u>服务器基于每个模型的关注力值和提供的更新值来更新参数</u>。
     - 也就是我们可以计算每个用户对服务器的影响，从而扩展得到每个用户对服务器模型每一层的影响

4. 本文的主要工作

   - 提出了测量客户端贡献量的方法，并且与使用shapley值的方法进行了比较
   - 证明我们所提出的模型对数据的数量和质量更为敏感，并可以用与客户端之间的相互比较
   - 证明训练过程中每个客户端的贡献量能够实时获取到，较低的计算复杂性

### 相关工作

> 将此贡献激励机制应用于根据代理商贡献分配佣金的情况

**A. 联邦学习**

1. 联邦学习解决问题的总结性话语

   联邦学习可以解决不受保护的大规模私有数据问题，在不交换大量数据的情况下完成设备的更新学习。

**B. 贡献与关注力机制**

2. 什么叫贡献机制？

   通过<u>测量不同agent对全局优化的贡献来分配不同激励</u>的方法

3. 本文采用了Shaoxiong Ji 所研究的关注力机制并提出的一种新的模型聚合方法

   - 考虑了各个客户端模型对全局模型的贡献
   - 在服务器聚合阶段使用了优化技术

   - 该方法<u>最小化了服务器和客户端之间的权重距离（？）</u>，并提高了沟通效率

### 方法论

**A. 联邦学习**

> 使用了联邦学习的基础架构，采用了FedAvg进行联邦优化

​	<img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301110527176.png" alt="image-2021030111052717" style="zoom:60%;" />

**B. 关注力聚合**

> 使用了Shaoxiong Ji 等人提出的FedAtt算法（优于FedAvg和FedSGD）

1. FedAtt算法是如何运行的？

   - 服务器通过公式(1)计算每层参数的关注力值$\alpha$，并将他们分发给客户端。

     > e的各层的范数差异次方求和，每层参数的注意力值为该层参数的范数差异占全部的多少

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301112434769.png" alt="image-20210301112434769" style="zoom:70%;" />

     > 其中$s_k^l$表示：<u>中心模型的第l层参数</u> 与 <u>当前客户端k的第l层参数</u> 的p范数差异

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301113528059.png" alt="image-20210301113528059" style="zoom:70%;" />

   - 之后【各个客户端】将每层参数（相对中心模型的更新量）乘以对应的关注力值，用来更新中心模型

     > 对于选中更新的m个客户端，我们计算得到每次迭代后中心模型的最新参数值。

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301114154297.png" alt="image-20210301114154297" style="zoom:70%;" />

     > 考虑到隐私保护，增加随机机制，最终更新参数的结果如下

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301115242717.png" alt="image-20210301115242717" style="zoom:70%;" />

2. **FedAtt算法的伪代码**

   <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301125548657.png" alt="image-20210301125548657" style="zoom:60%;" />

**C. 客户端贡献**`

3. 如何测量客户端的贡献

   - <u>客户端对服务器的贡献（影响） 基于</u> 客户端每层参数的<u>关注力值</u>进行计算，此处公式表示**客户端k在第t轮次中的贡献量**

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301130155435.png" alt="image-20210301130155435" style="zoom:67%;" />

     - 前部分值对应`B.关注力聚合`中模型参数更新公式的后半部分值部分
     - 后部分$\gamma$为遗忘系数，用于控制之前断点的影响，在多次迭代之后影响应该降低

   - 对于未参与某轮次t的客户端贡献量，其值与前一轮次相同

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301130855113.png" alt="image-2021030113085513" style="zoom:80%;" />

   - 最后对所有客户端的${aft}_t$值使用<u>MinMaxScaler正则化限制范围</u>，然后使用<u>softmax回归得到贡献量值</u>

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301131223317.png" alt="image-20210301131223317" style="zoom:80%;" />

4. 测量客户端贡献的伪代码

   <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301131304017.png" alt="image-20210301131304017" style="zoom:60%;" />

### 实验

1. 实验基本内容

   - 实验环境：Ubuntu18.04 LTS，后端使用pytorch的GPU版本，使用NVIDIA GTX1660Ti进行GPU加速

   - 使用LSTM模型中更小的基于GRU的客户端语言模型（采用RNN处理模型），通过testing perplexity来代表模型评估的效果

   - 使用FedAtt算法在Penn Treebank数据集上进行模型训练，之后比较估计各个客户端的贡献量估计值

   - 在这其中也与使用shapely value的结果进行比较，看哪种贡献测量方法更有效

   - 参数表

     <img src="A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301134644775.png" alt="image-20210301134644775" style="zoom:67%;" />

2. 实验具体内容和结果

   | 实验 | 1) 正常：数据随机分布并分发给所有客户端                      | 2) 修改最后四个客户端的数据集，变成随机的语句序列（认为是脏数据）`数据质量敏感性` | 3) 减少最后四个客户端70%的数据 `数据量敏感性`                | 4) 减少倒数3、4客户端30%的数据，最后两个客户端70%的数据`数据量的相对敏感性` |
   | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | 结果 | 每个客户端的（贡献率）偏差不大，只有个别较高较低；并且本文提出的贡献量测量方法和用shapley value的结果差不多 | 两种度量方法都可以识别出这些较差的客户端（数据），并给它们较小的贡献量 | 这四个特殊对待的客户端的贡献值降低了，但在shapely value度量估计中没有明显变化 | 本文的度量方法能够反映数据量的减少量，但shapely value不能    |
   | 图表 | ![image-20210301134039634](A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301134039634.png) | ![image-20210301134052637](A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301134052637.png) | ![image-20210301134117641](A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301134117641.png) | ![image-20210301134126791](A%20Real-time%20Contribution%20Measurement%20Method%20for.assets/image-20210301134126791.png) |

3. 实验结果总结

   - 本文提出的贡献量测量方法**能够合理公平地测量出各个客户端的贡献量**，并且相比shapely value<u>对于数据量大小和数据质量有更高的敏感性，评价更为准确</u>

   - 因此可以利用该方法合理建立一个评价数据大小、数据质量等指标敏感性的测量机制，该方法对数据进行了评估和训练，计算结果实时快速，贡献率反映准确。