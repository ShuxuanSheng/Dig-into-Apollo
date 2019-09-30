# Dig into Apollo - Introduction ![GitHub](https://img.shields.io/github/license/daohu527/Dig-into-Apollo.svg?style=popout)

## Table of Contents
- [简介](#introduction)
    - [定位](#localization)
    - [感知](#perception)
    - [规划](#planning)
    - [控制](#control)
- [目录结构](#content)
- [编译](#compile)

<a name="localization" />

## 简介
apollo是百度的自动驾驶开源框架，根据自动驾驶的功能划分为不同的模块，下面会根据目录结构和功能模块分别介绍和学习"apollo模块"。下面简单介绍下各个模块的作用：  

<a name="introduction" />

#### 定位
首先是定位,知道汽车在哪里,这里的定位可能涉及方方面面,比如GPS,但是GPS的精度只有米级别,有下面几个场景会不太合适,比如你在桥洞里,GPS信号不好的情况,另外还有一种情况是,停车的时候,你要知道前后车的距离,另外比如在雪天或者路况复杂的情况,这些情况下,仅有的GPS信号可能不能满足我们的要求,因此无人车又增加了激光雷达来测量周围环境的距离,而且可以精确到厘米。  
因此产生了高精地图的需求,高精地图可以把周围的3D环境都记录下来,这样我们就可以通过3D图像来匹配周围的场景,来找到自己的位置,而且高精地图还可以记录比地图多的多的东西,比如红绿灯的位置,交通标志,左转还是右转道.通过高精地图我们不仅可以知道道路情况,还可以知道车辆需要获取的一些其他信息,让车辆知道自己的实时位置。   

<a name="perception" />

#### 感知
然后是感知,我们总是希望车辆行驶在马路中间,这样更加安全,这就需要追踪到道路的路牙线,而道路随时会出现拐弯,那么追踪路牙线就用到了图像处理技术,另外还要感知到什么是车辆,什么是行人,主要涉及到图像的语义分割,感知是自动驾驶中最难,而且最具有挑战性的一块,因为只有感知到周围的行人,车辆,以及突发状况,才能为后面规划线路。  

<a name="planning" />

#### 规划
规划,目前已经知道当前道路情况,而且也已经感知到前面的车辆或者行人,如何去规划我们的行驶线路呢,这里需要解决的就是2个点之间的线路,而且行驶中途可能会出现新的情况,又需要重新规划线路,还有一种情况是,通过高精地图,我们已经知道前面需要转弯了,我们可以提前调整线路来适应这种需求。  

<a name="control" />

#### 控制
控制,现在已经规划出了一条线路,剩下的就是控制汽车,按照已经规划好的线路行驶,而且如果遇到突发状况,需要立即停车,而且控制汽车能够按照预定的线路不会出现很大的偏离,这就是控制要做的事情。  

<a name="content" />

## 目录结构
apollo是一个全栈的自动驾驶框架，下面是整个apollo代码的目录结构，主要是按照功能模块划分：  
```
|-cyber 消息中间件，替换ros作为消息层
|-docker 容器相关
|-docs 文档相关
|-modules 自动驾驶模块，主要的定位，预测，感知，规划都在这里
    |-calibration 校准，主要用于传感器坐标的校准，用于感知模块做传感器融合
    |-canbus 通讯总线，工业领域的标准总线，鉴于工业界的保守，我估计后面会有新的总线来取代
    |-common
    |-contrib
    |-control 控制模块，根据planning生成的路径对车辆轨迹进行控制，再底层就是发送命令到can总线，实现车辆的控制。
    |-data 地图等生成好的数据放在这里（其他数据待补充）
    |-dreamview 仿真，能够对自动驾驶过程中的数据进行回放，其他厂家也有推出一些仿真平台，后面有机会再介绍下
    |-drivers 雷达，lidar，GPS, canbus，camera等驱动
    |-guardian 监护程序？
    |-localization 定位，获取汽车的当前位置
    |-map 地图模块
    |-monitor 监控模块，主要是监控汽车状态，并且记录，用于故障定位，健康检查等
    |-perception 感知，获取汽车当前的环境，行人，车辆，红绿灯等，给planning模块规划线路
    |-planning 规划，针对感知到的情况，对路径做规划，短期规划，只规划100-200M的距离，生成好的路径给control模块
    |-prediction 预测，属于感知模块，对运动物体的轨迹做预测
    |-routing 导航线路，就是百度地图上查询2点之间的线路，生成的线路短期规划还是planning模块
    |-third_party_perception 第三方感知模块
    |-tools 工具，这里面的工具倒是很多，后面再详细介绍下
    |-transform 转换，主要是？
    |-v2x 顾名思义就vehicle-to-everything，其希望实现车辆与一切可能影响车辆的实体实现信息交互，目的是减少事故发生，减缓交通拥堵，降低环境污染以及提供其他信息服务.
|-scripts 脚本
|-third_party 第三方库
|-tools 工具目录，基本就是个空目录
```

<a name="compile" />

## 编译
apollo采用的是bazel来进行编译，因为我对JAVA的maven比较熟，因此对maven的包管理的功能觉得特别好用，第一能解决包自动下载，第二解决依赖传递的问题，第三解决了编译的问题。也就是说你只要引用对应的包，就可以把包的依赖全部解决。  
而bazel就是c++对应的编译管理，bazel主要是通过WORKSPACE和BUILD文件来进行编译。  
