# Notes: Multi-view Convolutional Neural Networks for 3D Shape Recognition

Pre-trained CNN models, data, and the complete source code to reproduce the results in the paper are available at http://vis-www.cs.umass.edu/mvcnn

## Abstract 

+ 3D形状（shapes）应该用3D格式来描述比如体素（voxel grid）或者多边形网格（polygon mesh）；还是可以用基于视角的描述
> should 3D shapes be represented with descriptors operating on their native 3D formats, such as voxel grid or polygon mesh, or can they be effectively represented with view-based descriptors?

+ 主要思想：用一个3D shape的多个视角的2D图片单独训练一个CNN网络，然后用单个视角的图片或者多个视角的图片作为测试输入，都可以达到比现有网络更高的准确率

---

## 1、Introduce

+ 使用体素表达（voxel representation）来训练网络识别3D形状，第一个直接使用3D数据的尝试
> Z. Wu, S. Song, A. Khosla, F. Yu, L. Zhang, X. Tang, and J. Xiao. 3D ShapeNets: A deep representation for volumetric shape modeling. In Proc. CVPR, 2015.

+ 然而，一个违反直觉的结果是，用从3D形状渲染出来的2D图片进行训练比直接用3D效果要好。也许是因为采样的3D数据不那么符合实际的物体。而其中一个原因是2D数据处理起来更有效率。第二个原因是我们可以使用先进的图像描述子和巨大的图像数据库。
+ 文章中指出，即使是使用普通结构的网络，用2D view images训练，识别率也比直接用3D voxel的网络要好；但是文章还提出了一种融合多个视角的方法，大概就是多加了一个层使得可以自动学习不同视角图片的权重。所以叫 multi-view CNN
> At test time a 3D shape is rendered from 12 different views and are passed thorough NN1 to extract view based features. These are then pooled across views and passed through
CNN2 to obtain a compact shape descriptor.

---

## 2、Related Work

+ 形状描述子（shape descriptors）能分成两个种类：1）、直接工作在3D表述的物体，例如polygon meshes、 voxel-based discretizations、 point clouds。2）、通过2D投影来描述3D物体比如：implicit surfaces、view-based descriptors

+ 其他的3D描述子（hand-designed）：1）、histograms 2）、bag-of-features models 3）、还有一下其他不懂的描述子 4）、LightField descriptor：从物体不同视角的轮廓（silhouettes）提取傅里叶描述子

+ 处理3D数据面临的问题：1）、数据库很小。ModelNet 有150Kshapes；ModelNet40 有4Kshapes共40个类），而ImageNet就有1000,0000张图片。2）、3D描述子更高维，使得分类器容易过拟合。（curse of demensionality）

+ 相比之下，view-based 描述子有以下的优点：1）、相对低维度。2）、对于3D形状缺陷（3D representation artifacts）更加鲁棒。

+ 之前有过一些其他的基于view-based的3D 物体识别网络(1.*H. Murase and S. K. Nayar. Visual earning and recognition of 3-D objects from appearance. 14(1), 1995*),以前把基于view-based的描述结合起来的方法通常很简单：1）、使用从不同视角提取的特征进行成对的详尽比较；2）、直接把各个视角提取的特征按顺序链接起来。

+ 这个网络通过一个叫 view-pooling 的层使得从各个视角提取出的特征可以有效地结合起来。
> In contrast our multi-view CNN architecture learns to recognize 3D shapes from views of the shapes using image-based CNNs but in the context of other views via a view-pooling layer. As a result, information from multiple views is effectively accumulated into a single, compact shape descriptor.

---

## 3、Method

### 3.1 Input：A Multi-view Representation

+ 3D数据库大部分是存储成polygon meshes（多边形网格），也就是一堆用边连起来的点构成面的集合。（ModelNet 3D 数据集就是用的这种储存方法）这里使用了叫Phong reflection Model 来获取渲染图（rendered views）：B. T. Phong. Illumination for computer generated pictures.Commun. ACM, 18(6), 1975.

+ 两种设置获取不同角度的图片：1）、假设所有形状已经对齐到某一个坐标轴。设置12个环形角度，中心是计算mesh face centers的加权平均。 2）、不假设对齐坐标系，设置20个虚拟相机，每个相机获取0,90,180,270度4张图片，一共获得80个角度。

+ 通过透视投影获得2D图片，像素的颜色由多边形网格顶点的反射强度的内插决定（这句话不是很理解）

### 3.2 Recognition with Multi-view Representation

+ 这边论文使用 Fisher vectors 来获取2D view的描述子，和用CNN（VGG-M)获取，（对每一个view进行提取，这里还没有进行不同view的结合，只是单纯地把scores加起来）。效果当然是CNN效果更好

+ 分类时，每个view当做一个训练样本，测试时，把12个view的值加起来然后取最大的值对应的类

+ 检索，使用了一个公式来衡量两个shape之间的距离（distance）。但是由于此时还没有合并不同view的features，使用这个公式需要计算Nx × Ny 对features之间的距离。

### 3.3 Multi-view CNN：Learning 同Aggregate views

+ 使用view-pooling layer来将不同views获得的信息组合成一个3D shape descriptor。 view-pooling 大概就是类似一个max-pooling，max-pool之后的features vectors 再通过一个小网络进行融合

+ 使用 multi-view 也可以用在普通2D图片的任务上，就是把同一张照片jittering之后从多个角度进行判别。有点类似data augment 的作用

+ 而对于检索（retrieval），使用马氏距离作为标准可以提高检索成功率。关于马氏距离，[这里](#https://blog.csdn.net/lanbing510/article/details/8758651)说得比较详细

---

## 4.Experiment

### 4.1. 3D shape Classification and Retrieval

+ 使用Princeton ModelNet dataset:  [The Princeton ModelNet](#http://modelnet.cs.princeton.edu/);包括了662个种类的共127,915个3D CAD model；另外一个40种类的子集ModelNet40 有12,311个shapes
+ 使用saliency map， backward 到每一个view的2D图片，可以看到某些view的图片的权重会比较大。（联想到shapenet的 Next-Best-View prediction，这个网络应该也可以完成这个任务

### 4.2 Sketch Recongnition:Jittering Revisited

+ 使用data jittering 或者说是 data augmentation，通过变换原始图片来获取更多的训练样本，可以达到降低过拟合的效果。
+ 这篇论文还通过view-pooling层来aggregate descriptors来提升2D图片的识别率。一个变换当成一个view

### 4.3 Sketch-based 3D Shape Retrieval

+ 3D检索，通过sketch来检索3D shapes
