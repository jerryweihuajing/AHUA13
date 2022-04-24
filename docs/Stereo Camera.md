# Stereo Camera

- real-sense作光源，自研图像传感器采图的采集方案
- 同行：钰立微，intel-realsense，indemind，inuitive（双目芯片）
- 一般的CPUGPU跑双目稠密匹配算法都会很吃力，只有ASIC芯片做硬化才能让双目的性能达到预期，算法IP化

## 1 Stereo Matching

### 1.1 Sparse Matching

#### 1.1.1 L-K Optical Flow

发现约束后的特征点存在一定的偏差，考虑使用基于块匹配的方式加以搜索：

快匹配具体的策略是在预先匹配的右图的特征点的左右各一段像素块内，基于块匹配的策略寻找最优匹配

探索了不同匹配算子对匹配效果的影响

探索了匹配像素块的尺寸和目标区域像素块的尺寸对匹配效果的影响

探索了不同特征匹配方式的效果

### 1.2 Dense Matching

仿真数据集来看SGM算法效果好于ADC？不加散斑的情况下

### 1.2.1 SGM



### 1.2.2 ADC



## 2 Stereo Calibration

### 2.1 Off-line Calibration



### 2.2 Self-Calibration




