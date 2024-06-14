

[TOC]



# Lidar + Camera

这部分主要是用于调研Lidar与Camera联合建图



## ImMesh(lidar + imu)

整理ImMesh中的demo以及实现思路



ImMesh

- real-time的mesh重建
- 运行效率+mesh重建的准确率 与现有方法的对比 ( 这里不知道mesh重建的精确度是如何对比的 )
- 潜在应用: (1) 点云强化 (2) mesh图的渲染



其他mesh的重建方法

- offline
  1. 基于Possion方法
  2. 三角剖分方法

- online



关于ImMesh上面与其他重建方法的对比

- 数据集的选择 真实数据 + 虚拟数据(包括 结构化场景+非结构化场景)
- 评价指标
  - 平衡性 fairness
  - 准确性 correctness

在评价中没有关于色彩信息是否准确的衡量标准



ImMesh中的上色部分可以直接基于height来实现，在加色彩信息的部分是只有实际的渲染时间。



建图部分使用的是 voxel + mesh，可以直接用其生成depth图像。voxel即体素(体素就是在3D空间中的最小空间，类似与2D平面中的像素点)，mesh我的个人理解就是一个面，跟论文中说的trianglar facet是一个东西(三角形的面)

![image-20240407211446720](figure/image-20240407211446720.png)

- 定位：将lidar scan转换到了一个global frame中，雷达的点云数据就可以作为新的三角mesh的顶点(具体的方法在VoxelMap文章中)。主要作用是 (1)保证lidar点云的准确 (2)提供一个plane方便3D点投影到2D点提高建图速率
- 建图：以voxel为单位进行处理

首先就先介绍一个voxel中的mesh平面的形成

- 顶点的选取：对于一个voxel来说，会有很多Lidar扫描到的点落到这个voxel中，这些点本身就是通过反射得到的，所以点的位置如果准确的话(这里就需要依靠odometry的准确度)，是可以描述出一种轮廓来的。对这些点进行处理(downsample以及限制距离)可以选择出一些点可以作为mesh的顶点，这也是后续处理的基础。
    - 注意：这里一个voxel中顶点不是仅仅选取落到整个voxel中的点，这种会导致的情况是voxel中的mesh平面与其他mesh平面之间是没有边连接的，如下图。所以本文使用的方法是选择与这个voxel中的点距离小于一个固定值的点(会降重)，最后形成一个顶点集合$V_i$

![image-20240407201613402](figure/image-20240407201613402.png)

- voxel级别的mesh/facet的形成(直接使用上面的点进行生成，并且是现在2D平面中生成)
    - 3D点转到2D平面: 2D Delaunay triangulation方法可以直接在2D 平面上生成三角形(我一直将这里的mesh认为是三角形的面，即facet)。顶点集合$V_i$中的点会全部投影到平面上(localization系统中找到了这个2D平面)，之后将2D的mesh转换到3D中形成3D mesh。时间效率可以从O(n^2)变成O(nlog(n))。

![image-20240407204213280](figure/image-20240407204213280.png)

- 更新

    ​	所谓的更新就是在lidar不断工作的过程中，会不断地得到新的点。这些新的点如何去更新已经建立好的mesh图。本文给出来的方法就是 pull + commit + push过程。

    - pull : 将之前的点生成的mesh/facet全部读取出来。对于一个voxel中的所有点(包括新得到的点/以及一些扩张的边缘点)也会读取，重新生成mesh(是重新生成voxel中的所有mesh)。
    - commit：根据新生成mesh与之前的比较，没有的增添，消失的删除。
    - push 将要删除的面与增加的面得到之后，将他们对应的顶点进行删除/增加之后即可。



****



## R3live(lidar + camera + imu)

![image-20240408135033542](./figure/image-20240408135033542.png)

LIO(FAST-LIO)用于构造点云地图(也就是描述整个场景的几何形状)，VIO用于地图渲染(也就是给点云提供色彩信息)。点云地图中最小单位是point,其保存在voxel(voxel作为一个小的容器可以包含一部分的点云数据)。一帧新的点云获取之后，会将其分到对应的voxel中。

- LIO 使用的是降低point-to-plane的残差来估计状态(在FAST-LIO2中提出的概念)。新来一帧数据，估计出位姿之后，其包含的点在实际环境中的哪一个voxel就被确定了。

**PS: 文章中没有明确写后面的tracked map points是怎么得到的，但是看流程图上面的解释应该是从LIO生成的点云地图中读取的，毕竟LIO是要比VIO先开始工作的。**



**重点是VIO的使用**

依据是一个点的色彩是这个点的固有属性，相机的旋转与平移不会影响色彩。估计位姿采用流程为两步 (1) 最小化 frame-to-frame 的重投影误差 (2) 最小化 frame-to-map 光度误差

- frame-to-frame

  光流找帧与帧之间的特征点，然后PnP最小化投影误差

  ![image-20240408131354052](./figure/image-20240408131354052.png)

  上面是使用visual方式得到的R,t观测值，IMU得到一个先验的状态值，用ESIKF卡尔曼滤波做了一个融合即VIO的位姿估计。

- frame-to-map(这里通过VIO的计算就应该可以得到了当前图像的R，t。那么就可以使用这个信息将global map中的信息投影到当前图像上，知道其对应的像素点，即可以得到颜色信息)

  - 所谓的光度误差就是点在global map中的颜色(即使用之前帧渲染出来的颜色)与当前帧中观测到的颜色之间的差值。下图就是将上一步上得到的Tracked map point投影到当前帧，即得到了这个点对应的像素位置，用周围像素点的RGB值做插值就可以得到在当前帧中这个点的颜色信息。

    ![image-20240408132810567](./figure/image-20240408132810567.png)

  - 对误差表达Taylor展开-因为其中包含了(1) 当前帧位姿 (2)地图点位置 (3)地图点颜色，所以最小化误差的时候可以将这三者进行一个联合优化。这样即实现了地图点颜色的更新，并且当前帧的位姿与地图点位置更加准确。被优化后的当前帧位姿作为下一帧VIO/LIO的起始状态。





## LVI-SAM

Vins mono与LIO-SAM的结合体，使用起来的效果还不错。

1. VIO使用LIO获取深度信息
2. 回环检测 VIO先检测再使用LIO辅助判断







## Kimera | Kimera2(camera+imu)

框架不断地在更新中，但是没有使用lidar数据，主要解决如下三个问题

1. 机器人位置		->      定位
2. 机器人周围环境       ->       建图(3d mesh)
3. 周围环境中有什么   ->       在mesh中加入语义标识(即度量语义)







## RTabMap

RTAB-MAP是经典的工作，并且已经开源。前端做的不够好，但是后端做的相当可以。由于RTAB-Map可以针对大场景+长时间任务，提出的部分要求是:

- Multisession mapping : 
- Online processing : (1) 新的传感器信息输入之后 -> output的时间相对输入时间要有最大的延迟 （2）由于可能存在其他模块，所以实际使用中还需要考虑其占用的计算资源



RTAB‐Map的核心是内存管理，独立于使用的里程计 —— 无论是视觉、雷达、还是wheel里程计亦或者是混合输入都是可以的。所以其是可以直接lidar数据输入的











## FC-Hetero

Fast and Autonomous Aerial Reconstruction Using a LiDAR-Visual Heterogeneous Multi-UAV System。一个很好的框架，2024的IROS，但是论文还是没有发出来。多机多传感器并且还是异构的形式。



这里还提到了一种新的数据结构

https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8838740/







## LiDAR4D (Lidar-only)

​	用nerf(神经辐射场)去做了一个动态场景的视角合成(时空视角) - 即可以显示一个物体在场景中的位置信息与时间信息。

特点主要有如下三种: 

- multi-planar
- grid
- ray-drop 



文章需要解决的问题主要有三个

1. 动态场景重建

2. 雷达数据是大尺度的并且稀疏

3. 为了最后生成场景的真实 - 需要保留强度与射线特性(虽然不理解但是消融实验中其表现的确实好)


- 输入: 雷达点云(每一帧点云有对应的位姿信息以及时间戳信息)，点云上对应的点包含了xyz以及强度信息。

- 输出: (1)对于整个动态场景的重建(但是这里没有形成一套完整的重建流程 即一个数据集中的车辆在前进，其周围的所有的场景都可以被重建出来) (2) 输入一个位姿以及时间 可以输出对应的雷达点云信息



补充信息:

​	在这种论文中会提到一个camera与lidar之间稀疏性的比较: 因为camera扫描到的信息更多(因为相机扫描方式得到的点数据会被雷达扫描得到的更加稠密，但是相机受到基线影响比较大，在距离上不能与雷达相比，精度也不算高)



### 运行教程

1. 安装conda https://www.eriktse.com/technology/1008.html

2. 之后按照github上面的教程进行

​	在运行的时候提示了本机安装的cuda11.3与conda中使用的cuda12.1不匹配，故删除cuda11.3，再次运行时提示nvcc: not found，个人感觉应该是没有cuda导致，在本机重新安装cuda11.8(对应的命令应该为pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu118)。

```python
git clone https://github.com/ispc-lab/LiDAR4D.git
cd LiDAR4D

conda create -n lidar4d python=3.9
conda activate lidar4d

# PyTorch
# CUDA 12.1
pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu121
# CUDA 11.8
# pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu118
# CUDA <= 11.7
# pip install torch==2.0.0 torchvision torchaudio

# Dependencies
pip install -r requirements.txt
```

- 但是我本机环境中为了运行其他SLAM算法将gcc降低到了7.5，在安装tiny-cuda-nn出现了fatal error: filesystem: No such file or directory       #include <filesystem>的错误，将其修改为<experimental/filesystem>也解决不了问题，故继续将整体算法更改到docker环境中，避免了修改gcc版本后影响其他算法运行。
    - 使用<experimental/filesystem>的错误如下:[**error: no instance of constructor nlohmann::basic_json::basic_json [with ObjectType=std::map, ArrayType=std::vector, StringType=std::string, BooleanType=__nv_bool, NumberIntegerType=int64_t, NumberUnsignedType=uint64_t, NumberFloatType=double, AllocatorType=std::allocator, JSONSerializer=nlohmann::adl_serializer, BinaryType=std::vector>\] matches the argument list argument types are: (__nv_bool) detected during: instantiation of void __gnu_cxx::new_allocator<_Tp>::construct(_Up \*, _Args &&...) [with _Tp=nlohmann::basic_json>>, _Up=nlohmann::basic_json>>, _Args=<__nv_bool &>]**](https://www.google.com/search?sca_esv=fa55d09ee79ad1d1&sca_upv=1&sxsrf=ACQVn0_FSQTqJZQR1b3LQoHJwmFA1_jcEQ:1713791914932&q=error:+no+instance+of+constructor+nlohmann::basic_json::basic_json+[with+ObjectType%3Dstd::map,+ArrayType%3Dstd::vector,+StringType%3Dstd::string,+BooleanType%3D__nv_bool,+NumberIntegerType%3Dint64_t,+NumberUnsignedType%3Duint64_t,+NumberFloatType%3Ddouble,+AllocatorType%3Dstd::allocator,+JSONSerializer%3Dnlohmann::adl_serializer,+BinaryType%3Dstd::vector>]+matches+the+argument+list+argument+types+are:+(__nv_bool)+detected+during:+instantiation+of+void+__gnu_cxx::new_allocator<_Tp>::construct(_Up+*,+_Args+%26%26...)+[with+_Tp%3Dnlohmann::basic_json>>,+_Up%3Dnlohmann::basic_json>>,+_Args%3D<__nv_bool+%26>]&sa=X&ved=2ahUKEwj6rruy9NWFAxUdr1YBHQlwBU0QgwN6BAg0EAE)

```python
git clone --recursive https://github.com/nvlabs/tiny-cuda-nn
cd tiny-cuda-nn/bindings/torch
python setup.py install
```

- docker选用nvidia/cuda:11.8.0-cudnn8-devel-ubuntu20.04。在docker中安装conda来方便python库的下载(因为我没怎么运行过python算法，想着在docker中继续使用conda可以方便配置环境)。docker中安装conda与本机安装没有区别。docker中运行剩余命令基本没有问题，除了在运行preprocess_data.sh文件中出现了libX11.so.6与OSError: libGL.so.1的错误，通过下面的命令可以解决：

    - apt-get install libx11-dev 解决 OSError: libX11.so.6: cannot open shared object file: No such file or directory

    - apt-**get** **update** && apt-**get** install libgl1  解决 OSError: libGL.so.1: cannot open shared object file: No such file or directory

        

- 运行bash run_kitti_lidar4d.sh后开始训练数据(我还以为是直接有pt文件可以直接输出，难道是nerf里面不能直接使用.pt文件么，本人也是对于整个python以及nerf一窍不通，哈哈)，这是正常的情况么？总之一共训练了639轮，然后输出结果。

![image-20240423163215694](figure/image-20240423163215694.png)



下载数据集，即方框中给出来的部分，然后放到data对应的文件夹中。

![image-20240423162845558](figure/image-20240423162845558.png)





相关的nerf/3DGS https://zhuanlan.zhihu.com/p/690749208

https://arxiv.org/pdf/2403.11367v1.pdf 3DGS使用在重定位上























































