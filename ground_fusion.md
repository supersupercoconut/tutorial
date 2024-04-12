# Ground_fusion | lvi_sam 指南

****

## 1. superpoint 使用

在feature tracker部分将光流替换成为superpoint+superglue作为工程上的尝试。使用方法是使用superpoint+supointglue上面来实现。因为是在vins为基础进行修改，所以应该先确定光流本身的使用以及在vins中光流的功能。

- 光流法

    - 输入 prev_img |  cur_img  | prev_pts  | cur_pts (cur_pts维数应该与prev_pts的size相同)

        ```CPP
        	cv::Mat prev_img cur_img
            vector<cv::Point2f> prev_pts cur_pts
            vector<uchar> status;
            vector<float> err;
        ```

    - 光流的使用

        1. status用于表示上一帧中的特征点在这当前帧中有没有被跟踪到(对应的一定是上一帧!!)，所以status中的size应该与prev_pts相同
        2. 对于没有跟踪到的点 status=0，并且cur_pts对应的特征点的位置也被计算出来了
        3. status prev_pts cur_pts 的size大小是一样的 | 这样逻辑上是通顺的 -> 当前帧上的特征点就应该是包含成功被跟踪到的点，但不是所有点都被跟踪到了，随着运行时间越来越长，跟踪到的特征点数量会不够，所以为了保证特征点数量需要再从这帧图像中读取一些角点来保证数量

    





## 2. docker使用

这里还是使用openvins中提到的ubuntu20.04+ros的docker image来进行环境配置，并且将本地目录挂载到docker容器中(这个人image在实现可视化方面配置的比较好，其他docker image或许可以修改bug实现同样的功能，但是这种是最好用的方式)。已经实现了在docker中运行



## 3. 算法环境配置

最容易出问题的还是pcl库的使用-这里其实不需要自己配置pcl库。虽然ground_fusion中说pcl应该是1.11，lvi_sam说应该是1.8，但其实这些算法都可以使用ros中自己安装的pcl1.10来运行。





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







目前关于这个lidar与imu一起使用的调研

- r3live
- immesh

https://blog.csdn.net/lovely_yoshino/article/details/126572997

ROS中的tf工具 

https://blog.csdn.net/wilylcyu/article/details/51724966



这个对于体素解释的很清楚

https://blog.csdn.net/a_eastern/article/details/107508861?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3-107508861-blog-121698677.235%5Ev43%5Epc_blog_bottom_relevance_base1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3-107508861-blog-121698677.235%5Ev43%5Epc_blog_bottom_relevance_base1&utm_relevant_index=6

这个也是对于voxel的解释

https://blog.csdn.net/m0_47163076/article/details/121698677







