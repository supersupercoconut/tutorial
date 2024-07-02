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



- [ ] 想想自己之前的那个多机联合初始化的实验应该怎么做

  



去年寒假的时候就看到了这个taichi语言，但是直到现在也只有一个SLAM方面的demo: taichiSLAM。我之前只是想使用这个工具来实现是一个可视化的工具，而且目前大家都在使用C++中的ros的rviz，不知道这个taichi 能不能实现我的需求

PS： taichi好像可以与mesh联动，但是不知道效果怎么样(反正效果很好玩，看起来不错的样子)





关于数据集中的标定算法

![image-20240616105637445](figure/image-20240616105637445.png)





# Canny-VO: Visual Odometry with RGB-D Cameras based on Geometric 3D-2D Edge Alignment



































