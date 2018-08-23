# 对 *PointNet: Deep Learning on Point Sets for 3D Classification and Segmentation*这篇论文的学习笔记

3D voxel grids ：3D体素网格（具体得再查一下）
collections of images ：具体也不知道是什么东西，把一个三个场景用很多的2D图片表示
permutation invariance ： 置换不变性
rigid motion ： 刚性运动
perturbation ： 扰动
exhibits strong performance on par or even better than state of the art.：跟最先进的技术表现一样或者更好
meshes ： 网眼
aggregate : 聚集（aggregate point features by max pooling）

## 2、Related Work
> 这里提到了几种处理3D数据的方法
+ 1、Volumetric CNNs:将3D卷积网络应用在体素外形（voxelized shapes）。无法解决由于数据稀疏（data sparsity）造成的分辨率问题和计算量
+ 2、Multiview CNNs：多视角CNN；尝试将3D点云排列成2D图像，然后应用2D的卷积神经网络；取得一些在形状分类（shape classification）和复原任务（retrieval tasks）。
+ 3、Spectral CNNS：光谱CNNs?
+ 4、Feature-based DNNs：通过提取传统的外形特征将3D数据转换成向量，然后用全连接层分类。被提取的特征的表达能力所限制


## 4、Deep Learning on Point Sets
### 4、1 多维点集的特点
+ 无序（unordered）：与二维的像素或者体积网格的体素数组不同，点云（point cloud）是无序的点，也就是说，我们的网络需要有对N个输入点的 N！次的置换不变性
+ 点间影响（interaction among points）：点并不是互相独立的。相邻的点可以组成一个有意义的子集。所以网络需要能够捕捉局部结构
+ 转换不变性（invariance under transformations）：对于同样一个点集，旋转或者转换等操作不能影响输出

### 4\2 PointNet Architecture
+ 处理数据的无序性有三种手段，1）、将输入排序到一个统一的顺序。2）、把输入当成序列来训练RNN，并且通过把序列任意可能排序来增强数据。3）、通过对称函数（symmetric function）来聚集信息，对称函数即无论输入的顺序如何，输出都不变。例如 + 运算和 * 运算。这也是这篇论文采用的办法

### 4\3 Theoretical Analysis
+ 证明此网络可以近似所有的连续集函数，只要该出足够大的神经元数量（证明部分不是很懂）

## 5、Experiment
### 5\1
+ 提到了一种 mIoU 的衡量不知道是指的什么

## 6、supplementrary
+ 提到了在进行part segmentation的时候，将局部特征，也就是底层的特征和全局特征（最终的maxpool输出）连接在一起，然后一起进行MLP得到scores