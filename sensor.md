# Sensor



## GPS

GPS协议有 NMEA 和 UBlox

- 主流 GPS 分为两种协议 NMEA 和 UBlox。NMEA 以 $ 开头 * 结束以字符串类型输出，UBlox 主要是以二进制输出，有专门的帧头，具体的协议解析要看相关文档。输出信息主要有用的数据是位置也就是经纬度、海拔高度、速度和相关精度。协议中一般会把这些关键数据放在一帧中方便使用。



对于RTK，其对应的协议为 RTCM

- 个人对于RTK的理解是 —— 基站端 gnss模块 决定发送RTCM数据 (对于CRTK 9ps，其中的GPS芯片是zed-9p，在ucenter中的message view确定数据即可)。对于移动端的gnss模块(不走飞控)，其需要通过一定的方式接受到RTCM数据，并与自己获取的GPS数据一起解算，最终将数据发送给飞控。飞控从串口中获取数据，并发送给电脑
  - **飞控本身并不会做GPS数据计算，只是作为数据中转**




1. 对于CRTK 9ps, 设置RTK模式，所需要的数据如下

<img src="./figure/image-20240911003800243.png" alt="image-20240911003800243" style="zoom:50%;" />

2. 两个9ps之间使用串口uart2通讯，这也需要设置
3. 将之前移动端的uart1给PX4的数据，可以设置给USB端口，即可以实现在ucenter中监控数据的输出，只要正常就OK



- 对于QGC而言，这是一个跟px4相关的部分，不用px4就不走QGC | 并且在QGC不管连接的是基站还是移动端的9ps，只要机载电脑启动了px4对应的launch文件，参数正常，其通过网络将传输给QGC，这样QGC上显示的就是飞控上面的设置。







## IMU

- 在武汉大学的的导航实验室中提供的信息, imu模块相当于是对一个运行物体的一些信息做测量（加速度或者角速度等），这些信息都是一种时域信息，转换到频率中就可以利用奈奎斯特采样频率进行分析。imu的采样频率应该是被测量数据最高频率的两倍以上才能进行测量。但实际工程应用中一般取5～10倍。而对于IMU采样，为了确保不因为离散化采样的信息损失而对惯导精度造成任何伤害，往往会采用10~20倍（甚至更高）的原始采样率。因此，只要条件允许，IMU的原始采样率应直接使用该器件的最高采样率，具体取决于载体动态情况，对于车载不能低于200Hz。**实际做imu递推的时候并不需要这么高频处理**



1. https://www.bilibili.com/read/cv13336033/?from=search&spm_id_from=333.337.0.0

2. https://www.cnblogs.com/zoneofmine/p/10853096.html





fastlio直接使用第一帧的imu做为world系，但是在vins-fusion中，处理过程就还有做重力与z轴对齐的过程

- fastlio中是不管初始状态的Imu是如何放置的，只让后续帧的Imu能对应转换到第一帧上来就行
- vins-fusion中不仅做了重力与z轴的对齐，并且在这里过程中旋转关系不应该牵扯到z轴的旋转(因为重力对齐只有xy轴旋转即可，即不需要yaw角有变换——这个yaw角说的是旋转矩阵对应的yaw角)，所以后续对这个yaw值又进行量修正





PS：在惯性导航中存在着双矢量定姿的方法，但是在slam中感觉都没有类似的操作

1. https://blog.csdn.net/weixin_42918498/article/details/124826651?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-5-124826651-blog-134489622.235v43pc_blog_bottom_relevance_base5&spm=1001.2101.3001.4242.4&utm_relevant_index=8



在写递推过程中的时候考虑这个blog  https://blog.csdn.net/u011341856/article/details/114262451



基于流型的kalman https://blog.csdn.net/woyaomaishu2/article/details/132912533



### IMU坐标系

- imu对准ENU坐标系 —— 实际上使用的就是双矢量定姿方法(其对应的是计算两个固定坐标系之间的变换关系)
    - 双矢量对准的意思应该是使用两个量来进行对准
    - 解析双矢量方法 —— 使用重力加速度以及地球自转角速度来处计算 | 间接双矢量方法 —— 只使用重力加速度测量值 ( 两个时刻 ) 来计算, 感觉其更适合mems imu，因为对于这种imu，静止下的角速度值不知道是测量出来的地球自转角速度或者是角度值测量噪声

先考虑其是静基座对准的方法
