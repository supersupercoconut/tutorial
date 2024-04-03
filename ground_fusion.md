# Ground_fusion | lvi_sam 指南

****

## 1. superpoint 使用

在feature tracker部分将光流替换成为superpoint作为工程上的尝试。使用方法是使用superpoint的libtorch版本，实现在cpp中调用superpoint





## 2. docker使用

​	这里还是使用openvins中提到的ubuntu20.04+ros的docker image来进行环境配置，并且将本地目录挂载到docker容器中(这个人image在实现可视化方面配置的比较好，其他docker image或许可以修改bug实现同样的功能，但是这种是最好用的方式)。



## 3. 算法环境配置

​	最容易出问题的还是pcl库的使用-这里其实不需要自己配置pcl库。虽然ground_fusion中说pcl应该是1.11，lvi_sam说应该是1.8，但其实这些算法都可以使用ros中自己安装的pcl1.10来运行。





## vins_node

定义节点, 所有发布的话题都自动加上本节点的名称

```cpp
ros::Subscriber sub_imu = n.subscribe(IMU_TOPIC, 5000, imu_callback, ros::TransportHints().tcpNoDelay()); // ros::TransportHints().tcpNoDelay()提示要ros快速处理，方便实时操作
```

自己设置一个rviz，然后保存下来，想使用的时候直接加载就好了 | 这样就直接可以加载之前保存好的rviz，不需要手动输入坐标系、需要订阅的话题等等

```
<node name="rvizvisualisation" pkg="rviz" type="rviz" output="log" args="-d /home/supercoconut/Myfile/have_a_try.rviz" />
```





使用ros::NodeHandle nh("~"),发布的话题名字为:节点名字+话题名字







没来得及看的链接

- https://github.com/castacks/tartanvo
- https://github.com/sair-lab/AirVO?tab=readme-ov-file
- https://github.com/ercbunny/open_vins?tab=readme-ov-file









