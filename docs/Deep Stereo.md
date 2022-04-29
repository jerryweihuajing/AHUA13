# Deep Stereo

双目深度学习大概有两种模式：

前两步相同，特征提取→代价计算

1 构建一个多维的视差空间，利用GT进行三线性插值，最终下采样到原始尺寸视差图

2 直接通过左右一致性检测来反复迭代，得到视差图



MSN属于第一种情况



## Overview

[A Survey on Deep Learning Techniques for Stereo-based Depth Estimation](https://arxiv.org/pdf/2006.02535.pdf)

测试场景分为两类，一类环境光比较正常的场景（Baseline），另一类为环境光比较复杂的场景（Chanllenge）：

![image-20220428093936378](resources/Deep%20Stereo/image-20220428093936378.png)

### 1 AnyNet

[Anytime Stereo Image Depth Estimation on Mobile Devices](http://export.arxiv.org/pdf/1810.11408v1)

它是一个四阶段网络，以从粗到细的方式构建3Dcost volumes 。第一阶段通过搜索小视差范围来估计低分辨率视差图。后续阶段使用残差学习来估计细化的视差图。

![image-20220428104757800](resources/Deep%20Stereo/image-20220428104757800.png)

Supervision Mode: Supervised

Cost Volume: 3D

Platform: Nvidia Tesla K40 GPU

Performance:  285ms

Memory: 0.232GB

Training Set: KITTI2015, KITTI2012

Evaluation (Bkg, Fg, Bkg+Fg): 9.46-11.6, 9.34-10.61



### 2 DeepPruner

[DeepPruner: Learning Efficient Stereo Matching via Differentiable PatchMatch](https://openaccess.thecvf.com/content_ICCV_2019/papers/Duggal_DeepPruner_Learning_Efficient_Stereo_Matching_via_Differentiable_PatchMatch_ICCV_2019_paper.pdf)

它将深度学习与匹配算法PatchMatch相结合，通过自适应地删减可能较大的对应搜索空间来加速推理。已经提出了两种变体：DeepPruner-Best和DeepPruner-Fast，前者将成本降低了4倍，后者将成本降低了8倍。

![image-20220428104720846](resources/Deep%20Stereo/image-20220428104720846.png)

#### DeepPruner-Best

Supervision Mode: Supervised

Cost Volume: 3D

Platform: Nvidia Tesla K40 GPU

Performance:  8343ms

Memory: 8.845GB

Training Set: KITTI2012+2015

Evaluation (Bkg, Fg, Bkg+Fg): 8.74-12.38



#### DeepPruner-Fast

Supervision Mode: Supervised

Cost Volume: 3D

Platform: Nvidia Tesla K40 GPU

Performance:  3920ms

Memory: 6.166GB

Training Set: KITTI2012+2015

Evaluation (Bkg, Fg, Bkg+Fg): 8.74-9.94



### 3 DispNet3

[Occlusions, Motion and Depth Boundaries with a Generic Network for Disparity, Optical Flow or Scene Flow Estimation](https://openaccess.thecvf.com/content_ECCV_2018/papers/Eddy_Ilg_Occlusions_Motion_and_ECCV_2018_paper.pdf)

是DispNet的一个改进版本，其中遮挡和视差图是联合估计的。

![image-20220428104952802](resources/Deep%20Stereo/image-20220428104952802.png)



Supervision Mode: Supervised

Cost Volume: 3D

Platform: NG

Performance:  NG

Memory: 10.953GB

Training Set: CSS-ft-KITTI, CSS-FlyingThings3D, css-FlyingThings3D

Evaluation (Bkg, Fg, Bkg+Fg): 8.38-11.11, 9.11-10.19, 9.29-10.61



### 4 GA-Net

[GA-Net: Guided Aggregation Net for End-to-end Stereo Matching](https://openaccess.thecvf.com/content_CVPR_2019/papers/Zhang_GA-Net_Guided_Aggregation_Net_for_End-To-End_Stereo_Matching_CVPR_2019_paper.pdf)

它将正则化块中的大量3D卷积层替换为（1）两个3D卷积层，（2）半全局聚合层（SGA）和（3）局部引导聚合层（LGA）。SGA和LGA层捕获局部和整体图像成本相关性。它们旨在提高具有挑战性的区域（如遮挡、大的无纹理/反射区域和薄结构）的精确度。

![image-20220428105039421](resources/Deep%20Stereo/image-20220428105039421.png)

Supervision Mode: Supervised

Cost Volume: 3D

Platform: Nvidia Tesla K40 GPU

Performance:  8336ms

Memory: 3.017GB

Training Set: KITTI2015, KITTI2012

Evaluation (Bkg, Fg, Bkg+Fg): 9.37-9.89, 9.98-11.55



### 5 HighResNet

[Hierarchical Deep Stereo Matching on High-resolution Images](https://openaccess.thecvf.com/content_CVPR_2019/papers/Yang_Hierarchical_Deep_Stereo_Matching_on_High-Resolution_Images_CVPR_2019_paper.pdf)

为了在对高分辨率图像进行操作时同时优化空间分辨率和深度分辨率，该方法使用从粗到精的层次结构以增量方式搜索对应关系。其分层设计还允许随时按需报告差异。

![image-20220428105259712](resources/Deep%20Stereo/image-20220428105259712.png)

Supervision Mode: Supervised

Cost Volume: 4D

Platform: Nvidia Tesla K40 GPU

Performance:  37ms

Memory: 0.474GB

Training Set: Middleburry, KITTI2015, ETH3D, HR-VS

Evaluation (Bkg, Fg, Bkg+Fg): 8.58-9.94



### 6 PSMNet

[Pyramid Stereo Matching Network](https://openaccess.thecvf.com/content_cvpr_2018/papers/Chang_Pyramid_Stereo_Matching_CVPR_2018_paper.pdf)

它逐步规范化了低分辨率4Dcost volumes ，根据特征金字塔进行估算。

![image-20220428105347367](resources/Deep%20Stereo/image-20220428105347367.png)

Supervision Mode: Supervised

Cost Volume: 4D

Platform: Nvidia Tesla K40 GPU

Performance: 1314ms

Memory: 1.9GB

Training Set: KITTI2015, KITTI2012

Evaluation (Bkg, Fg, Bkg+Fg): 9.8-10.1, 10.17-11.00



### 7 iResNet

[Learning for Disparity Estimation through Feature Constancy](https://openaccess.thecvf.com/content_cvpr_2018/papers/Liang_Learning_for_Disparity_CVPR_2018_paper.pdf)

初始视差和学习的特征用于计算特征恒常性映射，该映射衡量立体匹配的正确性。然后将初始视差图和特征恒常性图送入子网络进行视差细化。

![image-20220428105424465](resources/Deep%20Stereo/image-20220428105424465.png)

Supervision Mode: Supervised

Cost Volume: 3D

Platform: Nvidia Tesla K40 GPU

Performance: 939ms

Memory: 7.656GB

Training Set: KITTI2015, ROB

Evaluation (Bkg, Fg, Bkg+Fg): 45.87-61.72, 16.5-22.08



### 8 UnsupAdpt

[Unsupervised Adaptation for Deep Stereo](https://openaccess.thecvf.com/content_ICCV_2017/papers/Tonioni_Unsupervised_Adaptation_for_ICCV_2017_paper.pdf)

这是一种无监督的自适应方法，可以在没有任何基本事实信息的情况下进行微调。它首先使用KITTI 2012训练数据集对DispNet-Corr1D进行训练，然后将网络调整为KITTI 2015和Middlebury 2014。



Supervision Mode: Self-supervised

Cost Volume: 3D

Platform: NG

Performance: NG

Memory: NG

Training Set: KITTI2012 adapted to KITTI2015, Shadow-on-Truck

Evaluation (Bkg, Fg, Bkg+Fg): 8.52-10.78



### 9 SegStereo

[SegStereo: Exploiting Semantic Information for Disparity Estimation](https://openaccess.thecvf.com/content_ECCV_2018/papers/Guorun_Yang_SegStereo_Exploiting_Semantic_ECCV_2018_paper.pdf)

这是一种无监督视差估计方法，使用分割模板来指导视差估计。分割图和视差图都是通过端到端网络联合估计的。

![image-20220428105706974](resources/Deep%20Stereo/image-20220428105706974.png)

Supervision Mode: Self-supervised

Cost Volume: 3D

Platform: Nvidia GeForce RTX 2080

Performance: 195ms

Memory: below 12GB

Training Set: CityScapes

Evaluation (Bkg, Fg, Bkg+Fg): 9.03-10.54



## Conclusion

方法1至7由真值深度图监督，而方法8和9是自监督的。

****

| Name            | Supervision Mode | Cost Volume | Platform                | Perforamce (ms) | Memory (GB) | Resolution                                |                     Traning Set                      | Scene                     | Evaluation (Bkg, Fg, Bkg+Fg)       |
| --------------- | ---------------- | ----------- | ----------------------- | --------------- | ----------- | ----------------------------------------- | :--------------------------------------------------: | ------------------------- | ---------------------------------- |
| AnyNet          | Supervised       | 3D          | Nvidia Tesla K40 GPU    | 285             | 0.232       | 1240×376, 1242×375                        |                 KITTI2015, KITTI2012                 | Outdoor Driving           | 9.46-11.6, 9.34-10.61              |
| DeepPruner-Best | Supervised       | 3D          | Nvidia Tesla K40 GPU    | 8343            | 8.845       | 1240×376, 1242×375                        |                 KITTI2015+KITTI2012                  | Outdoor Driving           | 8.74-12.38                         |
| DeepPruner-Fast | Supervised       | 3D          | Nvidia Tesla K40 GPU    | 3920            | 6.166       | 1240×376, 1242×375                        |                 KITTI2015+KITTI2012                  | Outdoor Driving           | 8.74-9.94                          |
| DispNet3        | Supervised       | 3D          | Nvidia Tesla K40 GPU    | -               | 10.953      | 960×540                                   | CSS-ft-KITTI, CSS-FlyingThings3D, css-FlyingThings3D | Synthetic Driving         | 8.38-11.11, 9.11-10.19, 9.29-10.61 |
| GA-Net          | Supervised       | 4D          | Nvidia Tesla K40 GPU    | 8336            | 3.017       | 1240×376, 1242×375                        |                 KITTI2015, KITTI2012                 | Outdoor Driving           | 9.37-9.89, 9.98-11.55              |
| HighResNet      | Supervised       | 4D          | Nvidia Tesla K40 GPU    | 37              | 0.474       | 2948× 1988, 1240×376, 960×490, 2056× 2464 |         Middleburry, KITTI2015, ETH3D, HR-VS         | Synthetic/Real in/outdoor | 8.58-9.94                          |
| PSMNet          | Supervised       | 4D          | Nvidia Tesla K40 GPU    | 1314            | 1.9         | 1240×376, 1242×375                        |                 KITTI2015, KITTI2012                 | Outdoor Driving           | 9.8-10.1, 10.17-11.00              |
| iResNet         | Supervised       | 3D          | Nvidia Tesla K40 GPU    | 939             | 7.656       | 1240×376                                  |                    KITTI2015, ROB                    | Outdoor Driving           | 45.87-61.72, 16.5-22.08            |
| UnsupAdpt       | Self-supervised  | 3D          | -                       | -               | -           | 1240×376, 1242×375                        |   KITTI2012 adapted to KITTI2015, Shadow-on-Truck    | Outdoor Driving           | 8.52-10.78                         |
| SegStereo       | Self-supervised  | 3D          | Nvidia GeForce RTX 2080 | 195             | below 12GB  | 2048×1024                                 |                      CityScapes                      | Street scenes             | 9.03-10.54                         |

