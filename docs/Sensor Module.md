# Sensor Module

## Intel RealSense D400 Series

### Post-processing Filters

#### Filters Description

参考页面：[Depth Post-Processing for Intel® RealSense™ Depth Camera D400 Series](https://dev.intelrealsense.com/docs/depth-post-processing)

RealSense库配备有后处理算法来提升深度图质量并降低噪声。

##### Decimation filter

定义kernel的尺寸，通过下采样降低深度场景的复杂度。若尺寸为2-3pixel，取深度的中位数，若kernel的尺寸为4-8pixel将选择平均值作为计算的结果。

[1280,720]/3 -> [426.6666667, 240] -> [428,240]

经过处理后的深度图，内参会重新计算以补偿分辨率的改变。由于对深度图进行了下采样，也会具备一定的孔洞填充的作用。

| Controls  | Operation              |          Range          | Default   |
| :-------- | :--------------------- | :---------------------: | :-------- |
| magnitude | Decimation线性比例因子 | 离散整数：[2-8] (pixel) | 2 (pixel) |

##### Spatial Edge-Preserving Filter

基于论文实现：[Domain Transform for Edge-Aware Image and Video Processing](https://www.inf.ufrgs.br/~eslgastal/DomainTransform/Gastal_Oliveira_SIGGRAPH2011_Domain_Transform.pdf)

参考页面：[相关介绍](https://www.inf.ufrgs.br/~eslgastal/DomainTransform/)

关键特点：

1. 通过高阶变换保留一维边界实现空间滤波
2. 线性时间计算，并不影响参数的选择

滤波器执行一系列一维水平和垂直遍历或迭代，以增强重建数据的平滑度。

|   Controls   | Operation                                                    | Range                                                | Default  |
| :----------: | :----------------------------------------------------------- | :--------------------------------------------------- | :------: |
|  magnitude   | 滤波器迭代次数                                               | [1-5]                                                |    2     |
| smooth_alpha | 指数移动平均的Alpha因子，<br />Alpha = 1表示没有滤波 <br />Alpha = 0表示无限滤波 | [0.25-1]                                             |   0.5    |
| smooth_delta | 步长边界，确定用于保留“边缘”的阈值                           | 离散整数：[1-50]                                     |    20    |
|  hole_fill   | 在滤波迭代的过程中，对水平方向应用就地启发式对称空洞填充模式，旨在降低降低伪影的影响的同时不影响滤波的效果 | [0-5] 的范围映射到 [none,2,4,8,16,unlimited] (pixel) | 0 (none) |

##### Temporal Filter

时间滤波器旨在通过基于先前帧，操作每个像素值来改善深度数据的稳定性。

该滤波器对数据进行一次迭代，调整深度值的同时更新记录历史。万一像素的数据丢失或无效，滤波器可以使用用户定义的稳定性模式去决定丢失的数据值是应该被存储的数据校正。

请注意，由于过滤器依赖于历史数据，因此可能会引入可见的模糊/拖影伪像，因此最适合静态场景。

|      Controls       | Operation                                                    | Range                  |          Default          |
| :-----------------: | :----------------------------------------------------------- | :--------------------- | :-----------------------: |
|    smooth_alpha     | 指数移动平均的Alpha因子，<br />Alpha = 1 - 表示没有滤波 <br />Alpha = 0 - 表示无限滤波 | [0-1]                  |            0.4            |
|    smooth_delta     | 步长边界，确定用于保留“边缘”的阈值                           | 离散整数：[1-100]      |            20             |
| persistence_control | 一系列事先定义的伪码 (mask)来表示缺失的像素值是否会被上一帧的像素值取代，以及它是否会一直维持这个值:<br/>**Disabled**- 禁用，因此不会发生空洞填充<br/>**Valid in 8/8**- 启用，最后8帧当中这8个像素都有效 <br/>**Valid in 2/last 3** - 启用，最后3帧当中有2个像素有效<br/>**Valid in 2/last 4** - 启用，最后4帧当中有2个像素有效<br/>**Valid in 2/last 8** - 启用，最后8帧当中有2个像素有效<br/>**Valid in 1/last 2** - 启用，最后2帧当中有1个像素有效<br/>**Valid in 1/last 5** - 启用，最后5帧当中有1个像素有效<br/>**Valid in 1/last 8** - 启用，最后8帧当中有1个像素有效<br/>**Persist Indefinitely** - 启用，无论历史记录如何，该滤波器永远有效（最具有侵略性的滤波器） | [0-8] 根据描述映射对应 | 3 (**Valid in 2/last 4**) |

##### Holes Filling Filter

该过滤器实现了多种方法来纠正结果图像中的缺失数据。

过滤器获取四个立即像素“邻居”（上，下，左，右），并根据用户定义的规则选择其中之一。

| Controls | Operation                                                    | Range                 |          Default           |
| :------: | :----------------------------------------------------------- | :-------------------- | :------------------------: |
|   mode   | 控制将用于填充无效像素的数据<br /> **fill_from_left** - 使用左侧相邻像素的值填充孔<br/>**farest_from_around** - 使用距传感器最远的邻近像素的值<br/>**nearest_from_around** - 使用距传感器最近的邻近像素的值 | [0-2]根据描述映射对应 | 1 (**farest_from_around**) |

#### Design and Implementation

后处理模块被封装到独立的处理模块中，这些模块满足以下关键要求：

1. 同步/异步调用
2. 内部框架内存/生命周期管理

过滤器能够接收和处理来自不同来源的帧，尽管在一般情况下由于以下原因它不被调用：

- 每次识别到帧的新类型/来源时，都会产生性能开销，某些过滤器需要重新初始化
- 时间过滤器的有效性取决于要保留的帧历史，切换帧源会使保留的历史记录无效并使过滤器无法工作

因此，强烈建议为每个摄像机源建立和维护滤波流程。

过滤器保留原始数据并始终生成一个新的（过滤的）帧以传递。 新帧的重新生成允许在不同的消费者（线程）之间共享帧，而不会有数据被另一个用户覆盖的风险。

所有过滤器都支持离散和浮点输入数据格式：

1. 浮点输入由支持视差数据表示的 D400系列基于立体视觉的深度相机使用
2. 离散整型输入主要是SR300系列相机，在D400系列设备上也可以被使用（虽然不推荐）

#### Using Filters in application code

后处理模块设计开发并集成到了图像处理的流程当中，各种滤波器的应用顺序不存在强制约束。

同时以下为推荐的滤波器调用顺序，并且demo也是基于该顺序演示的：

**Post-processing**

> >**Depth Frame**
> >
> >> > **Decimation Filter**
> >> >
> >> > > > **Depth2Disparity Transform** 
> >> > > >
> >> > > > > > **Spatial Filter**
> >> > > > > >
> >> > > > > > > > **Temporal Filter** 
> >> > > > > > > >
> >> > > > > > > > > > **Disparity2Depth Transform** 
> >> > > > > > > > > >
> >> > > > > > > > > > > > **Hole Filling Filter**
> >> > > > > > > > > > > >
> >> > > > > > > > > > > > > > **Filtered Depth**

该后处理模块在D400系列可以使用，虽然滤波器的默认顺序已经事先定义，但认为可以将其切换开或关。

Demo和相关的工具已经将后处理的代码块植入了：

1. [RealSense-Viewer](https://github.com/IntelRealSense/librealsense/tree/master/tools/realsense-viewer)
2. [Depth Quality Tool](https://github.com/IntelRealSense/librealsense/tree/master/tools/depth-quality)
3. [Post-Processing Demo](https://github.com/IntelRealSense/librealsense/tree/master/examples/post-processing)