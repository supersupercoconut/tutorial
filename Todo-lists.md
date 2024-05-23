# Todo-lists

专门找一个写Todo的文档，省的写在其他地方不够清晰。

## corner case

- [ ] 对于传感器故障的情况，一般是怎么检测出来的？是不是跟师兄说的一样，要上升到信号处理的程度。

### wheel

- [x] ground-fusion论文(看过了，但是还是剩下的还是看源码)
- [ ] Pose estimation based on wheel speed anomaly detection in monocular visual-inertial slam即ground-fusion中的[19]
- [ ] Tightly-coupled monocular visual-odometric slam using wheels and a mems gyroscope即ground-fusion中的[18]
- [ ] 剑sir的论文还没有看

### vision

- [ ] let-net(这个还是我看到上一篇论文中看到的部分) | 这篇论文直到现在还是在更新，而且作者也知道是谁，github上他们也在写设计的逻辑是什么，感觉学习起来应该会更全面一些。对应的数据集全部都在下载了，我2T的硬盘估计里面装载的全部是数据集了。而且这里面提到的Tensorboard是什么东西(https://github.com/linyicheng1/LET-NET-Train/wiki/%E4%BB%A3%E7%A0%81%E9%80%BB%E8%BE%91%E4%BB%8B%E7%BB%8D)

## superpoint + superglue

- [ ] 这里就是分析如何提升精度+鲁棒性了，之前不是说了先提取一些点来看看效果么(现在有只使用sp提出来的点的方法，看起来效果还是不错的)

## vins-fusion 

- [ ] 代码逻辑还没有看完，这个初始化到底能不能与剩余部分进行分离(openvins中应该是分离的吧，因为有一个模块是ov_init)
- [ ] vins-fusion正好在淘宝上看到了类似的课程，这两天赶紧把所有的内容听完，这样就不耽误后续的进度了
- [ ] lvi-sam中的视觉部分到底是怎么实现的 \| ImMesh中的生成点云的部分是要依赖与lidar来实现的 —— 但是两者的实现原理并不是一样的，lidar里程计实现的效果完全是不一样的。 (那个不确定的平面对应的部分是协方差么)





## Multi-SLAM

- [ ] 多机初始化部分(下一次汇报估计就得汇报这个部分的内容)

    - kimera

    - vins那个课题组使用的方法

## else

- [x] 小六这边的交流会需要整理一下

- [ ] 想想自己之前的那个多机联合初始化的实验应该怎么做

  

1. 确定lvisam中的lidar作用
2. 确定相机中lvisam中的作用
3. ImMesh中所使用的里程计与lvisam中lidar的区别

参考链接

1. https://www.zhihu.com/people/gao-li-dong-62/posts
2. voxelmap中没有使用IMU —— 是只使用了lidar里程计的方法(看看点云地图转换成voxelmap——我感觉这一部分应该是转换成voxelmap的效率肯定是从0去修改这个lidar里程计更方便)





- [x] voxelmap与immesh中关于lidar数据的处理方式是不是一样的 | 其与lvisam中lidar odometry中使用的数据差距有多少

    - voxelmap与immesh中lidar数据的处理方式是一样的，但是livox的数据包含时间戳，但是velodyne没有时间戳信息 -- 没有找到合适的livox数据来运行voxelmap,单单从kitti数据集来看的话，都是可以直接运行的**(velodyne雷达数据中没有time属性，但程序可以运行)**

        

- [x] lvisam使用的里程计能不能直接使用voxelmap处理后的点云数据进行运行，这涉及到我能不能使用该方法来直接进行voxelmap的生成(这还不涉及到两者的优化方法不一样，一个是滑动窗口中非线性优化，另一个是直接ikf进行递推)
    - 不行，点云的配准方法是不一样的，在voxelmap里面，形成的这个体素+plane的地图应该是可以辅助scan-to-map的配准，然后计算出来位姿的，所以在这里要么从位姿估计到建图都使用voxelmap的操作，要么是即进行点云建图又进行voxelmap的操作



- [x] 尝试一下使用kitti的那个数据集运行 vins mono (感觉使用M2DGR会稍微好一些)

    - kitti数据集因为有odometry以及raw两种，odom数据集里面的点云数据只有10HZ, raw里面的imu数据会产生跳变，所以这两种方法都没有去进行测试**(raw+odom 都有转换好的数据集——进行了voxelmap的测试，都是ok的)**

    

- [ ] voxelmap++ 是不是能符合的要求 从生成的odom后才会进行plane的生成 —— 这里对于lvisam的lidar部分的改进起来是不是更简单一些，但是这种方法最不放心的再于其能不能进行mesh图的重建 | 但是voxelmap上有一个很大的问题是如果将其可视化，那么对应的rviz很卡，估计是有部分占用了很大计算资源(程序本身还在运行，但是可视化部分是不行了)

    

- [ ] 能不能在lvisam中同时进行点云地图+voxelmap的重建，voxelmap形成的点云地图只用于生成plane信息，进而使用这个信息来做mesh的重建。



- [ ] 更改lvisam中的雷达部分 —— 只要让lvisam在不考虑lidar+visual联合优化的部分，只使用雷达点云数据的深度信息进行初始化就可以

    

- [ ] 上一步基础上，将lidar+visual进行回环检测的部分也实现 | 现在感觉lvisam中互相的依赖关系还是没有整理清除



使用的数据集可以改为kitti或者M2DGR的数据集，lvisam自己的数据集在voxelmap上的运行一直出现问题，运行lvisam自身的数据集会出现很严重的漂移，所以按照voxelmap自己指出的数据集类型，运行kitti自带的数据集(即kitti_2011_09_26_drive_0084_synced.bag这个数据集 - 包含了单目图像 IMU 以及 lidar points数据)





voxelmap++ M2DGR walk.bag

![image-20240523222258937](figure/image-20240523222258937.png)

从中间过去，两边全是树

![image-20240523222321580](figure/image-20240523222321580.png)

从视觉上看貌似voxelmap没有voxelmap++效果好，but...voxelmap的可视化永远都在崩

![image-20240523224209389](figure/image-20240523224209389.png)
