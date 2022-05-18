# Calibration

## Photogrammetric Calibration

张氏标定法：[A Flexible New Technique for Camera Calibration](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/tr98-71.pdf)

- 2n*6的矩阵，每张图能提供2个约束，所以至少三张图能的得到张氏标定唯一解，若令倾斜系数为0，则可以增加一个约束求得解，或者说内参有5个自由度，外参有12-3个自由度，一共14个自由度，一组单应变换提供4个xy点8个约束，则两张图可以求解？
- 空间之中两个坐标系的刚体变换，一共有9个R和3个t需要求解，一共12个未知数。每个空间对应点能够提供xyz三个坐标维度的约束。
- 反投影误差（Reprojection Error）：在标定后每个三维点根据相机的投影矩阵计算得到的图像位置与实际图像位置之间总存在一个距离，这个距离的累加和就是重投影误差。
- 投影矩阵（Projection Matrix）：内参左乘外参。
- 捆绑调整（Bundle Adjustment）：对三维点坐标和相机位姿同时进行非线性优化。
- 线性优化（Linear Optimization）：例如最小二乘。
- 非线性优化（Non-linear Optimization）：梯度下降，牛顿法，LM法。
- 至少要三张不平行的标定板拍摄的图才可以得到标定唯一解，否则不能提供更多的约束，只是线性相关。
- 内外参数通过求得封闭初始解之后用极大似然估计LM法计算得到。
- 畸变参数的求解可以和内外参数交替进行迭代也可以直接设置初始值加入极大似然估计用LM算法解决。
- 棋盘格数量越多越好，迭代的计算趋于收敛。
- 棋盘格的位置45度左右最佳，太小无法提供约束，太大会出现投影缩减，不易于提取角点。

## Self-Calibration

港科大双目自标定：[High-Precision Online Markerless Stereo Extrinsic Calibration](https://uav.hkust.edu.hk/wordpress/wp-content/uploads/2016/09/iros2016yonggen.pdf)

