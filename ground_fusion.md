# Ground_fusion | lvi_sam 指南

****

## 1. superpoint 使用

在feature tracker部分将光流替换成为superpoint作为工程上的尝试。使用方法是使用superpoint的libtorch版本，实现在cpp中调用superpoint。

### nvidia驱动 | cuda | cudnn | libtorch | conda安装

**最需要注意的部分：可以在安装cuda | cudnn | libtorch之前先安装conda —> 这样可以在一个虚拟环境中完成环境的管理，后期想进行环境的替换或者移植到另一台设别上，也都是很方便的(给我一种docker的感觉—>但是conda更像是管理学习相关的虚拟环境，对于其他情况，其效果应该没有docker方便)**

首先是关于显卡驱动 | cuda | cudnn方面的解释(这里没有去使用anaconda去管理环境)

- 显卡驱动明显就是让图形化界显示的更好一些的手段——安装之后就可以使用nvidia-smi来获取driver以及支持的cuda最高版本信息(并不代表安装上了cuda)
- cuda 就是让系统更好的利用显卡设备
- cudnn 更好地利用显卡设备来处理深度学习
- libtorch 就是提供一种C++的方式来搭建模型来处理输数据(模型搭建好之后还是需要python自己训练好的模型权重)



在安装上：

1. nvidia驱动一直是一个比较简单的安装项。只需要自己在设置里找到对应的位置就可以直接安装。在安装过程中需要设置一个boot的密码，然后reboot。在reboot之后一定不要选择continue boot(也就是第一项)
    - 反正是reboot之后会出现四个选项，选择第一项就是continue boot(选择之后重新启动也没有完成显卡驱动安装 | 这次安装选择的是第二项) 
2. 安装cuda以及cudnn可以选择deb或者.run文件的安装方式，但是一定要注意对应的版本问题。这次安装的是cuda-11.8以及cudnn8(cuda的路径/usr/local/cuda-11.8)

    - cuda的安装完成之后要在bashrc中加入一些语句

    - cudnn的安装之后要发现查找cudnn.h或者cudnn_version.h都没有正常时，需要使用这个[教程](https://blog.csdn.net/weixin_39518984/article/details/120881546)中的命令sudo cp cuda/include/cudnn_version.h /usr/local/cuda/include/ 

![在这里插入图片描述](figure/3361fbc088234d61a9660950bb76afea.png)

3. libtorch的安装——这里的安装简单，-d即指定了安装路径，我已经习惯将其安装到/usr/local中了

```CPP
#! /bin/bash
wget -O LibTorch.zip https://download.pytorch.org/libtorch/cu102/libtorch-cxx11-abi-shared-with-deps-1.6.0.zip
sudo unzip LibTorch.zip -d /usr/local
```



参考链接:

1. [安装教程(但是缺少了最后在bashrc中需要添加的内容)](https://blog.csdn.net/qq_34972053/article/details/127689332?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-8-127689332-blog-119003405.235^v43^pc_blog_bottom_relevance_base5&spm=1001.2101.3001.4242.5&utm_relevant_index=11)

2. https://blog.csdn.net/h3c4lenovo/article/details/119003405

3. [cuda 卸载](https://blog.csdn.net/2301_77554343/article/details/134376103?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-134376103-blog-121158255.235^v43^pc_blog_bottom_relevance_base5&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

4. [卸载教程](https://blog.51cto.com/u_15905131/5918429)

5. [libtorch使用教程](https://blog.51cto.com/u_15088375/5735740)

    

cuda| cudnn官方教程

1. cudnn https://developer.nvidia.com/rdp/cudnn-archive 
2. cuda https://developer.nvidia.com/cuda-11-8-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_local




****



还是有很多不理解的地方的——conda中安装的环境怎么管理，| cmakelists.txt如何去找conda中的安装的cuda 



将模型运行到Cpp上还有的方法：onnx以及tensorRT

现在还有一个人的博客写的听精彩的 

https://arxiv.org/pdf/1711.02508.pdf

https://zhuanlan.zhihu.com/p/96465592

https://zhuanlan.zhihu.com/p/269257787

就是这个博客，很好看

https://sjtu-robotics.com/zh/blog/2024/hello-jacobian/



## 2. docker使用

这里还是使用openvins中提到的ubuntu20.04+ros的docker image来进行环境配置，并且将本地目录挂载到docker容器中(这个人image在实现可视化方面配置的比较好，其他docker image或许可以修改bug实现同样的功能，但是这种是最好用的方式)。



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



