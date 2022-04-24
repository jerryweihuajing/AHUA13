# Deep Stereo

双目深度学习大概有两种模式：

前两步相同，特征提取→代价计算

1 构建一个多维的视差空间，利用GT进行三线性插值，最终下采样到原始尺寸视差图

2 直接通过左右一致性检测来反复迭代，得到视差图



MSN属于第一种情况



## Overview

[A Survey on Deep Learning Techniques for Stereo-based Depth Estimation](https://arxiv.org/pdf/2006.02535.pdf)

### 1 AnyNet

它是一个四阶段网络，以从粗到细的方式构建3Dcost volumes 。第一阶段通过搜索小视差范围来估计低分辨率视差图。后续阶段使用残差学习来估计细化的视差图。



### 2 DeepPruner

它将深度学习与匹配算法PatchMatch相结合，通过自适应地删减可能较大的对应搜索空间来加速推理。已经提出了两种变体：DeepPruner-Best和DeepPruner-Fast，前者将成本降低了4倍，后者将成本降低了8倍。



### 3 DispNet3

是DispNet的一个改进版本，其中遮挡和视差图是联合估计的。



### 4 GANet

它将正则化块中的大量3D卷积层替换为（1）两个3D卷积层，（2）半全局聚合层（SGA）和（3）局部引导聚合层（LGA）。SGA和LGA层捕获局部和整体图像成本相关性。它们旨在提高具有挑战性的区域（如遮挡、大的无纹理/反射区域和薄结构）的精确度。



### 5 HighResNet

为了在对高分辨率图像进行操作时同时优化空间分辨率和深度分辨率，该方法使用从粗到精的层次结构以增量方式搜索对应关系。其分层设计还允许随时按需报告差异。



### 6 PSMNet

它逐步规范化了低分辨率4Dcost volumes ，根据特征金字塔进行估算。



### 7 iResNet

初始视差和学习的特征用于计算特征恒常性映射，该映射衡量立体匹配的正确性。然后将初始视差图和特征恒常性图送入子网络进行视差细化。



### 8 Unpadpt

这是一种无监督的自适应方法，可以在没有任何基本事实信息的情况下进行微调。它首先使用KITTI 2012训练数据集对DispNet-Corr1D[22]进行训练，然后将网络调整为KITTI 2015和Middlebury 2014。



### 9 SegStereo

这是一种无监督视差估计方法，使用分割模板来指导视差估计。分割图和视差图都是通过端到端网络联合估计的。



方法1至7由真值深度图监督，而方法8和9是自监督的。