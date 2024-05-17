





[TOC]



# Single-SLAM

对于单机SLAM，主要是调研一下其在处理corner case的应对策略。主要是针对跨越险阻中提供的数据。但是使用这个数据首先要处理的就是将对rosbag的读取换成读取excel数据读取(在vins上面是有这设置的，因为kitti数据集就是excel格式)

- vins mono 初始化 需要IMU的充分激励以及视觉变化

## 初始化

目前的一个思路是在暑假之前实现出一个多机系统的初始化框架出来-但是首先要完成的是关于vins-fusion框架初始化部分的观看-需要直到这种开源框架中的初始化部分是怎么实现的，之前有关于这个部分实现了open3D的colorICP的使用，实现了相对位姿的计算。



## 视觉

视觉上主要的使用有两个 (1) 定位上使用，使用特征点来定位 (2)



### 开源数据集

- M2DGR 数据集: 分析了当前slam算法可能存在的问题 (在采集的数据上运行SOTA的slam算法来测试结果，并且分析出现误差的原因)
  - 数据集包含的内容很多，六个环绕的鱼眼相机以及红外相机、事件相机都有。GNSS、IMU、雷达数据都包含了，但是没有/odom (M2DGR-plus也已经出来了)
- Ground-Challenge

# Multi-SLAM

## 初始化

多机之间的初始化

- 假定传感器是视觉+IMU传感器+UWB+lidar
- 场景为室内与室外环境



需要实现功能为 (都是一些常规任务，在这里很难提到自己的创新点——但是相比于多机系统，这也算是相当简单的框架了)

1. 常规的SLAM初始化 —— 传感器之间的对齐 | 建立自己的坐标系等等
2. 全局坐标系生成 得到global frame（PGO）可能要去看kimera-multi了(人家确实生成了一个global frame)
3. 点云等信息配准 —— 最后肯定是实现全局建图(目前在做的还是直接点云配准 ——这个思路太简单了)

4. 计算出来的相对位姿与什么真值做比较？

   

—— 实现整个模块之后来发文章(保底三区)



(1) 通讯

(2) 计算速度

(3) 场景中是否存在动态物体/或者纹理信息缺少/点云匹配时的Overlap信息缺少



后期每一个agent的累计误差的处理暂时不考虑 —— 先做实验判断哪个部分出现了问题(即先做实验尝试多机初始化会在哪里出现问题 )



还没有来的及看的链接

1. 一个我个人觉得非常好的链接 https://github.com/VIS4ROB-lab/decoSLAM 也是一个有名的实验室的产品 | 但是这里写的关于slam系统的东西有一点难写 | 但是对于slam通讯占用资源的检测、地图的冗余度、以及多机之间构造的这个优化框架是可以看的 —— 这里提到了shared map来避免每一架机的资源浪费
2. 如果想生成类似与众包地图的功能，这是不是多机应该有的东西
3. 手里还是有几个多机的框架没有仔细看的











eigen单独进行优化——在soc-cheap上实现功能(不使用ceres以及GTSAM)

UWB来解决尺度方面上的drift(比如在有GPS以及无GPS环境切换的过程中，使用1到多个UWB来降低drift)

- 之前的定位肯定是一直有误差存在的，在系统运行的过程中，不断地会加入量测信息，这时候的状态估计是结合两者(状态估计与量测)的协方差，使最终得出来的协方差信息一定是两者协方差的结合量。如果量测信息不准，就继续使用状态进行递推。如果新来的量测是准的，那么就可以使用该量测去降低当前状态估计的协方差，让当前状态更准——这不就让整个系统的不确定性下降了么。因为协方差代表的是真实值与估计值之间的误差，所以有效地使用量测可以抑制状态估计中的累积误差。





先验地图生成

- HCTO: Optimality-Aware LiDAR Inertial Odometry with Hybrid Continuous Time Optimization for Compact Wearable Mapping System(没开源)



基于先验地图的SLAM

- RAL 2023 SLICT: Multi-input Multi-scale Surfel-Based Lidar-Inertial Continuous-Time Odometry and Mapping(貌似之前看到过) | 已经开源了



动态光照

- AirVO —— 竟然也是应对动态光照的，还是存视觉的 (点线结合的)



全局回环+全局匹配(貌似比港大的 STD 效果还要好) | 全局定位很容易就会使用语义信息

- Outram: One-shot Global Localization via Triangulated Scene Graph and Global Outlier Pruning(也开源了)
- Segregator: Global Point Cloud Registration with Semantic and Geometric Cues(也开源了)





超出SLAM系统之外再上层——竟然还会出现防止绳子打结的论文(主要是发了TRO以及RSS )。

关于地图融合的——比如在多机任务中，会存在着很多重复区域

Magnetic Field-Aided Global Localization in Repetitive Environments(但是磁场辅助定位是不是太扯了) 







MCD 数据集 —— 用于解决不同区域特征不相同的情况 —— 提供先验点云 | 提供IMU UWB Lidar camera数据

- 雷达数据使用的是Livox 输出















