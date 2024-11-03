# SLAM

整理看到的Paper思路

## corner case detection

整理各种传感器的退化场景的检测方法 ——主要整理开源算法，相当于找到多种传感器失灵的情况。定性分析各种传感器失效比较简单，定量分析比较复杂

### lidar

主要由于insufficient geometric constraint(可能是某一个方向上的几何约束)

 **数据集**

1. Heterogeneous LiDAR Dataset for Benchmarking Robust Localization in Diverse Degenerate Scenarios | **将退化分成了平移退化以及旋转退化，但是没有定量分析一个场景是不是退化场景（都是强制指定一个场景中导致的退化形式）**
   - Waterways 水路 : 针对水面反射产生退化
   - Flat ground ：直接将lidar对应地面，这样其在平面足够大的情况，xy平移或者绕z轴旋转lidar会导致退化
2. 

**检测方法**

1. MM-LINS

   ![image-20241002221931525](./figure/image-20241002221931525.png)

2. 





## multi-sensor calibration(标定)

1. Heterogeneous LiDAR Dataset中包含了livox lidar以及 Ouster lidar与相机、imu进行了标定
   - **joint-lidar-camera-calib ：标定livox 与 camera**
   - imu_utils 标定imu



















## Paper reading with code

### VINS-fusion

整理在vins中遇到的处理逻辑，并且后续阅读groundfusion





### Ground-fusion



### MINS 

- 多传感器的思路，并且已经开源。其使用imu作为核心部分，其他传感器作为测量值，并且加入了传感器的时空标定。整体的**状态估计使用连续时间的状态模型**。算法使用基于流型的状态估计，并且使用动态克隆方法平衡精度以及计算负载(这个并不明白) | 在coco-lic中也是使用了B样条的插值方法来实现连续时间的状态估计。

- **该算法的lidar部分并没有直接使用livox的lidar...并且修改这部分并不容易 | coco-lic是支持livox的算法，不知道其是否可以在这里借鉴**

- 因为是使用的open_vins的标准，所以这里的四元数是JPL格式的四元数，VINS（Visual Inertial Navigation System） 中通常使用的是 Hamilton 四元数标准。这里会存在一定的区别！！





**pipeline**

![image-20241028160925306](figure/image-20241028160925306.png)

整理算法的实现思路

- 基于流型的插值—— 在没有传感器硬同步的时候使用该方法进行多种传感器的同步

- 初始化： 静态初始化 (IMU only) + 动态初始化  (IMU+wheel)

- 坐标系：一开始没有GNSS的时候，在初始化确定下来的world系中进行处理。当存在GNSS的时候，world系直接转移到东北天坐标系中。



**ros话题**

整理算法发布出来的rostopic以及实现自身数据集的整理部分







[PPCA-VINS](https://github.com/lnexenl/PPCA-VINS) 基于先验点云地图实现的vins-fusion，也是用一个作者团队开发的













目前就看到一个开源了比较多传感器数据集的dataset论文

### Ground-challenge

当前关于ground robot的数据集分析

- M2DGR

- M2DGR-plus

- OpenLORIS

- groundchallenge: 使用fastlio2充当真实值数据 | 关于录制的数据集按照场景进行划分（正常场景以及一些corner case下录制的数据集）| **并没有严格证明一个corner case就一定对定位算法产生多少影响**

    - office/room 对应的是正常场景，用于测试在正常环境中的SLAM算法
    - Visual Challenge: 亮度较低｜不同程序下的遮挡（甚至出现了全遮挡）| 快速运动（拍摄到的图像有一些模糊）
    - Wheel Odometer Challenge:  光滑平面 | rough road 运动路径出现震动
    - Motion Challenge:  提供了一些只旋转或者是之字形向前运动的场景，即认定整个运动变得困难

    ![image-20241018205332924](figure/image-20241018205332924.png)

    在groundchallenge中展示视觉上的障碍(**可以将室内室外录制出来的数据集都进行一种整理, 视觉上是最容易进行展示的情况**) 

<img src="figure/image-20241018210033493.png" alt="image-20241018210033493" style="zoom:50%;" />



### Task-driven SLAM Benchmarking 

Task-driven SLAM Benchmarking(主要是针对benchmark文章结构的整理) —— 这里也提到了一些其他的benckmark是如何实现的

- Task-driven
- 自身录制了数据集进行处理









































### Coco-lic阅读























### MINS代码阅读

已经在MINS上配置完成来自己的数据集，并且简单测试来定位精度，感觉还可以但是没有非线性优化的方法好。

TODO：

(1) 录制一版 包含joint_state的odom数据 | 测试算法精度



















***









**代码阅读**

整体上是依赖与之前的ov_core部分，但是不知道什么没有include  ov_core上面的头文件。

## 传感器

### IMU

1. 6轴传感器 三个轴的加速度+三个轴的角速度 -> 这样可以积分得到三个轴的上面的欧拉角

2. 9轴传感器 在6轴的基础上，多了三个磁力计 -> 在室内使用的效果很差

​	https://zhuanlan.zhihu.com/p/344884686



### lidar

一般来说lidar通过扫描出来的数据在尺度上比图像更大，但是数据没有相机稠密。

- 雷达数据一般包含了四个方面，也可以称其为四维即xyz+反射强度。xyz代表了扫描到的点的几何结构，反射强度可以从侧面解释物体的材质与距离。PCL的库中就有XYZI这种点云的数据格式(点云数据的整体反射率也可以衡量一个雷达性能好坏，一般会用10%的反射率的探测距离来说明)



Lidar的种类是很多的，并且Lidar的扫描结果是离散的——也就是使用lidar进行扫描的过程中，面对的物体轮廓上面只有一部分被扫描的到，每个点之间有一定的距离，所以称为离散扫描。所以在进行点云配准的时候，并不是point to point的对准(这一帧扫描到的点在下一帧中未必会被扫描到)。 

机械式雷达与livox这种非重复性的固态雷达相比： 机械式雷达生成一帧的过程中需要将探测部分旋转之后才能得到scan结果，故需要进行云哦的嗯补偿，否则会出现重影。还有一个很重要的点在与livox雷达在扫描过程中，是非重复性扫描——其意义就是机械式雷达静止在那里，每一次scan获取到的点云位置不变，而非重复扫描得到的点云数量会越来越多。



![image-20240520205047279](figure/image-20240520205047279.png)

关于雷达线数的说明(主要是针对机械雷达而言，固态雷达没有传统意义上的线数)

- 线数是在垂直分布的，而且并不是均匀分布，越靠近中间部分雷达的数据量就越多。

![image-20240521232232604](figure/image-20240521232232604.png)

参考连接：

1. https://zhuanlan.zhihu.com/p/102621881



#### lidar的退化环境

- 因为lidar是靠几何结构进行计算，在长直道的这种情况下, 几何结构又是很相似的部分。既计算出来的结果 —— body明显移动了，但是计算显示body没有移动



























不同型号雷达 最后对应的pcl中的点云信息有什么区别

现在正在使用的雷达有如下三种

(1) m2DGR 数据集/ lvisam数据集/kitti/跨越险阻/groundchallenge  velodyne 

(2) m2DGR-plus 数据集 Robosense 16(怎么感觉没有看过关于其的调研) 速腾型号的雷达

(3) mid70(固态雷达)即livox 主要分布在港大Immesh/voxelmap/r3live这种数据集上面，在开源代码中的处理基本完成可 

(4) mid360(暂时不需要-不需要调研这部分)



rs为国内的雷达厂家, 输出的雷达数据貌似只有XYZI，缺少了 ring以及timestamp. velodyne的数据类型好看，只需要看对应的源码里面是在怎么处理并且包含了什么信息即可。

- velodyne

  ​    

- livox





这个对于体素解释的很清楚

https://blog.csdn.net/a_eastern/article/details/107508861?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3-107508861-blog-121698677.235%5Ev43%5Epc_blog_bottom_relevance_base1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3-107508861-blog-121698677.235%5Ev43%5Epc_blog_bottom_relevance_base1&utm_relevant_index=6

这个也是对于voxel的解释

https://blog.csdn.net/m0_47163076/article/details/121698677







在fast-lio中一帧点云并不是直接用到odom的递推方程中的。因为Lidar扫描一帧点云是有一个time的偏差的，在实际使用时会将一段时间的点云数据转换成一镇SCAN，然后给odom做递推（以固定采样时间间隔作为一个SCAN进行处理，而不是将整个激光帧作为一个处理单位
如一帧点云采样时间为100ms，将20ms作为一个SCAN，即将一帧激光点云变成5个SCAN）



关于pcd中保存的数据，这里对应的xyz rgb信息，其中的rgb对应的是一个无符号整数类型，然后对应的是整个部分的rgb信息。

```
# .PCD v0.7 - Point Cloud Data file format
VERSION 0.7
FIELDS x y z rgb
SIZE 4 4 4 4
TYPE F F F U
COUNT 1 1 1 1
WIDTH 3925315
HEIGHT 1
VIEWPOINT 0 0 0 1 0 0 0
POINTS 3925315
DATA ascii
6.4672627 78.574593 -0.25317496 4282730555
10.256534 77.851814 1.1185876 4283524917
2.9052517 79.031265 0.20551738 4278453252
2.9047186 79.014641 -1.6342744 4281545532
```









