# Multi-SLAM

## initalization

首先是多机系统联合进行的初始化部分，需要确定出一个共同的坐标系出来进行处理。

> Swarm-SLAM: Sparse Decentralized Collaborative Simultaneous Localization and Mapping Framework for Multi-Robot Systems | RAL2024 | 回环检测 +  图优化

- 没有进行多机相互的初始化操作 |  **回环检测完成整个逻辑**
  - 回环检测计算出来确实是一个转换的R,t，应该能用于多机之间互相的坐标系计算上面

> Kimera-Multi: Robust, Distributed, Dense Metric-Semantic SLAM for Multi-Robot Systems | TRO 2022 | 

- **多机之间进行初始化(定义了统一坐标系), 并且代码开源**
  - 初始化 —— 假设A，B两个坐标系，利用多次的回环检测，计算两个坐标系之间的关系（具体涉及到了GNC以及截断最小二乘法、PGO) | 看起来比较简单，可以直接看源码了
    - 回环节点 即在一个坐标系下表示的坐标[x_1,y_1,z_1] = [R,t] [x_2,y_2,z_2]，这些xyz坐标虽然在不同的坐标系但是其表示的应该是相同点，所以找到足够的点(即足够的回环)，就能计算两个坐标系之间的转换关系



PS: 

(1) 为什么清华那个项目需要不断地计算当前位姿与动补之间的区别来着

(2) 在多机系统中大家比较的指标一般是什么 —— 都没有去说单独一个agent的定位精度的提升