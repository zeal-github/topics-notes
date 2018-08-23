# 这是一个用来备忘学习transfer learning的一些感悟

<font size=5>目录<br><font>
<font size=4 color=Aqua>
[一、基础概念](#标题一)<br>
[二、domain adaptation](#标题二)<br>
[三、A survey on transfer learning](#id1)
+ [1、Transfer learning 的定义](#id2)
+ [2、Transfer learning 的三个子集](#id3)
+ [3、四种迁移学习的方法](#id4)
+ [4、Transfer Bounds](#id5)
+ [5、Negative Transfer](#id6)
</font>

<a id="标题一"></a>

## 一、transfer learning 的基础概念，cs231这篇概述写得挺清楚的
[cs231n-transfer-learning](http://cs231n.github.io/transfer-learning/)
<a id="标题二"></a>

## 二、domain adaptation ： 域适配 
域不同但任务相同，且源域数据有标签，目标域数据没有标签或者很少数据有标签<br>
下面是一篇介绍了domain adaptation领域几篇论文的博客，包括【DeCAF、DDC、DAN、JAN】<br>
[听见下雨的声音;迁移学习之Domain-Adaptation](https://chenrudan.github.io/blog/2017/12/15/domainadaptation1.html)
<br><br>
下面是上文里提到的一份关于迁移学习很全面的资料。<br>
[迁移学习 Transfer Learning](#http://transferlearning.xyz/)

<a id="id1"></a>

## 三、一篇具有代表性的综述 [A survey on transfer learning](http://ieeexplore.ieee.org/abstract/document/5288526/)由知名的杨强团队发表。对迁移学习做出定义。下面是学习这篇论文的一些笔记。
<a id="id2"></a>
### 1、 Transfer learning 的定义
+ 给定source的域（domain）和学习任务，以及target的域和学习任务；迁移学习的目的在于利用从source中学习到的知识提高对target的预测函数的表现
+ Domain（域）包括了数据以及数据的边缘概率分布
+ learning task（学习任务）包括了标签的不同以及预测函数或者说P（Y|X）的不同
<br>

<a id="id3"></a>

### 2、transfer learning 的三个不同子集
+ Inductive Transfer Learning ：归纳式迁移学习；源和目标域相同，源任务和目标任务不同但是有联系
> 分两种情况：1）、源域的数据有标签；2）、源域的数据没有标签；大部分研究集中于前一种情况<br>

> 在这种问题下，有一种基于Instance 的transfer learning方法叫`TrAdaBoost`（boost：增强）--It attempts to iteratively reweight the source domain data to reduce the effect of the "bad" sourcedata<br>

> Feature-representation-transfer ： 找到好的特征表达，来minimize domain divergence;这种方法也经常用在多任务学习中。文中举出的集中方法也都是muti-tasks里面的<br>

> Parameter-transfer : 有 regularization framework 和  hierarchical Bayesian framework；<br>其中的正则化框架，大概就是通过某些正则化项，使其可以同时学到common parameters和specific parameters

+ Unsupervised Transfer Learning ：非监督迁移学习；源和目标的域和任务都不同；并且都是没有标签的
> 由于源和目标域的标签都是不可用的，关于这方面的研究还比较少（12年的论文）

> Self-taught clustering(STC)：feature-transfer 学习共同特征，用互信息量

> 这里提到了一种`sparse coding`稀疏编码的方法

+ Transductive Transfer Learning ： 直推式迁移学习；源和目标域不同但是有联系，任务相同；目标域的标签不能用

> 传统的机器学习中，transductive learning指的是所有的测试数据（未标记）的都要被训练时看到；而在这里我们强调任务必须相同，并且有一些未标记的数据在目标域中可以使用

<a id="id4"></a>

### 3、四种不同的基于 What to transfer 的迁移学习方法
+ Instance-based transfer learning （or instance transfer）：假设源域的部分数据可以用在目标域中学习，通过`reweighting`
+ Feature-representation-transfer ：学习一个更好的特征表示方法
+ Parameter-transfer ：源任务和目标任务共享`参数`或者`超参数的先验分布`
+ Relational-knowledge-transfer ：假设源域和目标域的数据是有相似之处的。Thus, the knowledge to be transferred is the relationship among the data. 这种方法主要处理的是有联系的域之间的迁移学习。数据是non-i.i.d(independent and identically distributed (i.i.d.) )。不同与其他方法，假设每一个域之间数据都是独立同分布的。这种方法目的在于迁移每一个域的数据中的联系。

<a id="id5"></a>

### 4、Transfer Bounds
+ 一个重要的问题，是要认识到迁移学习的能力局限。

<a id="id6"></a>

### 5、Negative Transfer
+ 一个经验性的结论就是，当源和目标的域和任务差别过大时，鲁莽地进行迁移学习反而会破坏目标任务的表现。所以如何衡量源和目标域的数据和任务之间的差别也是一个需要考虑的问题。
