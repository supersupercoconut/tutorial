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

- [ ] LEAP-VO: Long-term Effective Any Point Tracking for Visual Odometry (使用tracked point的方法来对动态物体进行去除，据说会比使用语义分割的方法更加节省算力) —— 光模型的训练就用了4张A100... 
- [x] 基于上面给出的LEAP-VO，之前的模型还有TartanVO以及DytanVO。这两种方法应该都是针对动态环境的方法并且不需要使用语义分割网络将物体分割出来
- [ ] let-net(这个还是我看到上一篇论文中看到的部分) | 这篇论文直到现在还是在更新，而且作者也知道是谁，github上他们也在写设计的逻辑是什么，感觉学习起来应该会更全面一些。对应的数据集全部都在下载了，我2T的硬盘估计里面装载的全部是数据集了。而且这里面提到的Tensorboard是什么东西(https://github.com/linyicheng1/LET-NET-Train/wiki/%E4%BB%A3%E7%A0%81%E9%80%BB%E8%BE%91%E4%BB%8B%E7%BB%8D)
- [ ] TartanVO(一直说深度学习的VO，但是我这里还没看过)

## superpoint + superglue

- [ ] 这里就是分析如何提升精度+鲁棒性了，之前不是说了先提取一些点来看看效果么

## vins-fusion 

- [ ] 代码逻辑还没有看完，这个初始化到底能不能与剩余部分进行分离(openvins中应该是分离的吧，因为有一个模块是ov_init)

## Multi-SLAM

- [ ] 多机初始化部分(下一次汇报估计就得汇报这个部分的内容)

    - kimera

    - vins那个课题组使用的方法


## else

- [x] kalman的作业，下一次以及上一次的作业都还没有写(还有问题在于这个使用量测来帮助imu的递推过程，真的可以实现精度的提升么)
- [x] 周三还有一个汇报的内容还没弄
- [x] NTU谢老师那里有一个slam-craft的汇报 时间1～1.5h小时 —— 里面介绍了不少我现在想看的论文









## 5.10 ~ 5.16

- [x] 点云到mesh的转换 
- [x] kalman 作业
- [x] groundfusion以及lvisam的代码阅读
- [ ] 继续推进多机的任务



 std_msgs 