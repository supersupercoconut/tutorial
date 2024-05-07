# Ground_fusion | vins-fusion 指南

****

## 1. superpoint 使用

在feature tracker部分将光流替换成为superpoint+superglue作为工程上的尝试。使用方法是使用superpoint+supointglue上面来实现。因为是在vins为基础进行修改，所以应该先确定光流本身的使用以及在vins中光流的功能。

- 光流法

    - 输入 prev_img |  cur_img  | prev_pts  | cur_pts (cur_pts维数应该与prev_pts的size相同)

        ```CPP
        /*
          使用的opencv函数为:calcOpticalFlowPyrLK()
            1.对于存在预测的情况(这里给的cur_pts_一定不能是空)
            cv::calcOpticalFlowPyrLK(prev_img, cur_img, prev_pts, cur_pts_ /*输出的追踪结果，允许给出初值*/, 
                lk_status, lk_err, cv::Size(21, 21) /*指定金字塔每层上的搜索窗口的size*/, 1 /*指定金字塔层数为2层*/, 
                cv::TermCriteria(cv::TermCriteria::COUNT+cv::TermCriteria::EPS, 30, 0.01) /*指定搜索的终止条件*/, 
                cv::OPTFLOW_USE_INITIAL_FLOW /*指定启用cur_pts_中给定的初值，以便加速搜索*/ );
        	
            2.不存在预测(对于cur_pts_的数据就没有要求)
            cv::calcOpticalFlowPyrLK(prev_img, cur_img, prev_pts, cur_pts_, 
                                     lk_status, lk_err, cv::Size(21, 21), 3);
        
                
          基本输入参数
          	cv::Mat prev_img cur_img
            vector<cv::Point2f> prev_pts cur_pts
            vector<uchar> status;
            vector<float> err;
        */ 
          
        
        
        ```

    - 光流的使用

        1. status 用于表示上一帧中的特征点在这当前帧中有没有被跟踪到 (对应的一定是上一帧!!)，所以status中的size应该与prev_pts相同
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















## 关于Vins-fusion匹配

1. 第一次处理IMU数据的时候，依靠IMU测量得到的重力加速度，将IMU系转换到world系中([参考链接](https://blog.csdn.net/hltt3838/article/details/109514591?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171342390616800215050781%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171342390616800215050781&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-109514591-null-null.142^v100^pc_search_result_base6&utm_term=Eigen%3A%3AQuaterniond%3A%3AFromTwoVectors%28ng1%2C%20ng2%29.toRotationMatrix%28%29%3B&spm=1018.2226.3001.4187) ) | 这是我能找到的最详细的解释了

<img src="figure/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hsdHQzODM4,size_16,color_FFFFFF,t_70.png" alt="img" style="zoom: 50%;" />







