# 3D ShapeNets: A Deep Representation for Volumetric Shapes

[项目地址](#http://3dshapenets.cs.princeton.edu/)

## Abstract

+ 2.5D depth sensors (e.g. Microsoft Kinect) 产生2.5D depth maps
+ 主要思想：用3D meshs 数据（`3D shapes`)，转换成3D volumetric 数据，然后对网络进行训练。训练之后的网络可以对 2.5D 数据（depth maps）图像进行 1）、分类； 2）、分类的同时进行重建； 3）、进行最佳视角的预测。
+ 本文同时还发布了一个教大型的3D 数据库叫做 ModelNet。
+ 本文的方法似乎是第一次提出对原始3D数据进行处理，虽然效果现在已经被大部分网络超过

## 1.Introduction

+ 本质上来说，这个网络是对2元变量，即3D体素方格的一个概率分布预测
+ 图形重建（shape completion）的主要任务是，给定一个单一角度的2.5D depth map， 然后进行3D结构的复原
+ 注意，这个网络是可以同时进行复原和识别的，所以我感觉单纯用识别率评判它是不公平的

## 2.Related Work

+ 3D shapes 识别的技术大部分是基于部分物体结合的，不变性较差且笨重
+ Next-Best-View problem(NBV) 是在运动传感器可以移动到新角度这个场景下提出来的（*W. Scott, G. Roth, and J.-F. Rivest. View planning for automated 3d object reconstruction inspection. ACM Computing Surveys, 2003*）
+ 使用互信息量来解决NBV问题，由这个提出(*J. Denzler and C. M. Brown. Information theoretic sensor data selection for active object recognition and state estimation. PAMI, 2002*)

## 3.3D ShapeNets

+ 用3D体素网格（voxel grid）中的二元变量来表示3D shape（1：体素在mesh 表面内部；0：体素在表面外部；网格大小：30x30x30）
+ 不使用任何的pooling层，虽然pooling可能会增加网络对于识别的不变性，但是会导致更严重的重建的不确定性

## 4. 2.5D Recognition and Reconstruction
### 4.1. View-based Sampling

+ 虽然是用3D voxel训练的模型，但是可以对2.5D depth maps进行物体识别，当然是要先对depth maps进行预处理（freespace 和 surface 划分为`X0`；occluded的voxel划分为`Xu`；识别就变成估计 p（y|X0）

+ Gibbs sampling:<br>
&emsp; 1. 把Xu初始化为随机值<br>
&emsp; 2. 把（X0，Xu）输入网络，得到一个 label y<br>
&emsp; 3. 对 x 进行 backward，保持X0不变，更新Xu；50个循环之后就得到shape completion以及对应的y

### 4.2. Next-Best-View Prediction

+ NBV的输入：观测到的体素X0（observed voxel），以及有限的角度列表{Vi}<br>
&emsp; 1. 原始的识别不确定性是对于y的条件概率的熵：H(p(y|X=X0))<br>
&emsp; 2. 当移动到另外一个角度Vi时，一些之前没有观测到的voxel Xu会变得可以观测；不同角度会导致不同Xu的可见性<br>
&emsp; 3. 然后会产生新的不确定性（熵）；目的就是减少熵之间的差，也就是互信息量

+ 大概过程（应该是这样）<br>
&emsp; 1. 通过原始depth map 产生3种最可能的3D 预测<br>
&emsp; 2. 移动到下一个角度，对于3D 预测（3D 结构），能够观测到的新的Xu.<br>
&emsp; 3. 再输入网络进行熵的计算
