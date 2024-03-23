# Ground_fusion 指南

- darknet 网络模型 c+cuda实现，用于在cpu | gpu上实现yolo模型的部署(ground-fusion中实现的yolo模型就是github中已经写好的方式，所以如果想实现superpoint只需要直到实现的方法是什么，然后使用现成的方法来加速功能实现——性能不好最后再优化就完事了，先实现比较重要)

    

- 之前看的那个report使用的是wrapper library 直接将要处理的部分转换为api进行操作。但是本文使用的时候也没有用Libtorch这些看起来比较复杂的库，而是直接去弄了一个库与其对应的msg

- 在图像的回调函数中使用input_image -> feature tracker 





### nvidia驱动 | cuda | cudnn | libtorch | conda安装

**最需要注意的部分：可以在安装cuda | cudnn | libtorch之前先安装conda —> 这样可以在一个虚拟环境中完成环境的管理，后期想进行环境的替换或者移植到另一台设别上，也都是很方便的(给我一种docker的感觉—>但是conda更像是管理学习相关的虚拟环境，对于其他情况，其效果应该没有docker方便)**

首先是关于显卡驱动 | cuda | cudnn方面的解释(因为这里没有去使用anaconda去管理环境)

- 显卡驱动明显就是让图形化界显示的更好一些的手段——安装之后就可以使用nvidia-smi来获取driver以及支持的cuda最高版本信息(并不代表安装上了cuda)
- cuda 就是让系统更好的利用显卡设备
- cudnn 更好地利用显卡设备来处理深度学习
- libtorch 就是提供一种C++的方式来搭建模型来处理输数据(模型搭建好之后还是需要python自己训练好的模型权重)



在安装上：

1. nvidia驱动一直是一个比较简单的安装项。只需要自己在设置里找到对应的位置就可以直接安装。在安装过程中需要设置一个boot的密码，然后reboot。在reboot之后一定不要选择continue boot(也就是第一项)
    - 反正是reboot之后会出现四个选项，选择第一项就是continue boot(选择之后重新启动也没有完成显卡驱动安装 | 这次安装选择的是第二项) 
2. 安装cuda以及cudnn可以选择deb或者.run文件的安装方式，但是一定要注意对应的版本问题。这次安装的是cuda-11.8以及cudnn8(cuda的路径/usr/local/cuda-11.8)

![在这里插入图片描述](figure/3361fbc088234d61a9660950bb76afea.png)

3. libtorch的安装——这里的安装简单，就是找一个版本直接安装 (虽然这里不是严格按照版本的对应关系安装的，但是可以work不出问题) | 直接使用如下的.sh文件安装的

```CPP
#! /bin/bash
wget -O LibTorch.zip https://download.pytorch.org/libtorch/cu102/libtorch-cxx11-abi-shared-with-deps-1.6.0.zip
sudo unzip LibTorch.zip -d /usr/local
```





参考链接:

1. [安装教程(但是缺少了最后在bashrc中需要添加的内容)](https://blog.csdn.net/qq_34972053/article/details/127689332?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-8-127689332-blog-119003405.235^v43^pc_blog_bottom_relevance_base5&spm=1001.2101.3001.4242.5&utm_relevant_index=11)
2. https://blog.csdn.net/h3c4lenovo/article/details/119003405

cuda| cudnn官方教程

1. cudnn https://developer.nvidia.com/rdp/cudnn-archive 
2. cuda https://developer.nvidia.com/cuda-11-8-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_local











一个小的提示: 显卡驱动与cuda还是两个东西的，毕竟不是所有人都是用来跑模型的



https://blog.51cto.com/u_15905131/5918429 卸载方法

我在使用过的过程中删除了自己安装的显卡驱动

- 这个还是可以写的，关于如何使用如何更新

https://blog.csdn.net/weixin_41010198/article/details/109367449



还有一种方法是使用https://github.com/RichExplor/CNN_VINS 这种方法是直接用Python的方法并且加入了numpy，比起来直接使用libtroch的方法看起来好不少 | 或者是使用libtorch自己的方法来实现功能

https://blog.51cto.com/u_15088375/5735740 解释的是；libtorch的基本解释





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



