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





### multi-sensor calibration

1. Heterogeneous LiDAR Dataset中包含了livox lidar以及 Ouster lidar与相机、imu进行了标定
   - **joint-lidar-camera-calib ：标定livox 与 camera** 
   - imu_utils 标定imu
2. 







## Paper reading

目前就看到一个开源了比较多传感器数据集的dataset论文

### Ground-challenge

(1) 主要在于传感器的数据录制上，其实并没有严格证明一个corner case就一定对定位算法产生多少影响



当前关于ground robot的数据集分析

- M2DGR

- M2DGR-plus

- OpenLORIS

- groundchallenge: 使用fastlio2充当真实值数据 | 关于录制的数据集按照场景进行划分（正常场景以及一些corner case下录制的数据集）

    - office/room 对应的是正常场景，用于测试在正常环境中的SLAM算法
    - Visual Challenge: 亮度较低｜不同程序下的遮挡（甚至出现了全遮挡）| 快速运动（拍摄到的图像有一些模糊）
    - Wheel Odometer Challenge:  光滑平面 | rough road 运动路径出现震动
    - Motion Challenge:  提供了一些只旋转或者是之字形向前运动的场景，即认定整个运动变得困难

    ![image-20241018205332924](figure/image-20241018205332924.png)

    在groundchallenge中展示视觉上的障碍(**可以将室内室外录制出来的数据集都进行一种整理, 视觉上是最容易进行展示的情况**) 

<img src="figure/image-20241018210033493.png" alt="image-20241018210033493" style="zoom:50%;" />

- Task-driven SLAM Benchmarking(主要是针对benchmark文章结构的整理) —— 这里也提到了一些其他的benckmark是如何实现的
  - Task-driven
  - 自身录制了数据集进行处理

