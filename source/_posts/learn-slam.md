---
title: learn-slam
date: 2020-04-02 20:18:33
tags:
- SLAM
---
# SLAM

本教程主要参考[古月ROS入门21讲](https://github.com/huchunxu/ros_21_tutorials)

## 基础概述

### 常用命令行

Ubuntu系统下常用

- cd
- pwd
- mkdir
- ls
- touch
- mv
- cp
- rm
- sudo

### 常用cmake指令

一般编译一个cmake工程, 要用如下代码:

```shell
mkdir build
cd build
cmake ..
make
```

如果编译完要安装的话, 需要

```shell
 sudo make install
```

下面是一些常用句法

```shell
# 声明要求的cmake最低版本
cmake_minimum_required( VERSION 2.8 )
# 声明一个cmake工程
project( HELLO )
# 添加一个可执行程序
add_executable( helloSLAM helloSLAME.cpp )
# 添加一个静态库
add_library( hello libHelloSLAM.cpp )
# 添加一个共享库
add_library( hello_shared SHARED libHelloSLAM.cpp)
# 将useHello 链接hello_shared库
add_executable( useHello useHello.cpp )
target_link_libraries( useHello hello_shared )
```

### ROS核心概念

#### Node(节点)

-- 执行单元

- 独立进程
- 不限语言
- 名称唯一

#### ROS Master(节点管理器)

-- 控制中心

- 提供命名和注册服务
- 管理Node
- 提供参数服务器

{% asset_img p1.png 'Node and ROS Master' %}

#### 通信机制

|特点|Topic/Message|Service|
|:-----:|:-----:|:-----:|
|同步性|异步|同步|
|通信模型|发布/订阅模型|请求/应答模型|
|反馈机制|无|有|
|实时性|弱|强|
|Node关系|多对多|一对多(只有一个Server)|
|适用场景|数据传输|某些需要完成又不用频繁调用的功能|

{% asset_img p2.png 'Topic/Message' %}
{% asset_img p3.png 'Service' %}

#### Parameter(参数)

全局使用的参数, 储存形式是字典

## 开发流程

工作空间workspace是存放工程的文件夹, 包括4个部分

- src: source space
- build: build space
- devel: development space
- install: install space

创建工作空间(以catkin_ws举例)

```shell
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
cd ~/catkin_ws
catkin_make
source devel/setup.bash
```

创建功能包(以test举例, 依赖是roscpp rospy)

```shell
cd ~/catkin_ws/src
catkin_create_pkg test roscpp rospy
```

编译工作空间中所有功能包

```shell
cd ~/catkin_ws
catkin_make
source ~/catkin-ws/devel/setup.bash
```

### 示例

#### 所有ROS初学者的第一个程序

```shell
roscore
rosrun turtlesim turtlesim_node
rosrun turtlesim turtle_teleop_key
```

{% asset_img p4.png 'bash' %}
{% asset_img p5.png 'turtlesim' %}

#### Topic/Message

1. 定义msg文件

    ```msg
    string name
    uint8 sex
    uint8 age

    uint8 unknown = 0
    uint8 male = 1
    uint8 female = 2
    ```

1. 在package.xml中添加功能包依赖

    ```xml
    <build_depend>message_generation</build_depend>
    <exec_depend>message_runtime</exec_depend>
    ```

1. 在CmakeLists.txt中添加编译选项

    ```cmake
    find_package( ... message_generation)
    add_message_files(FILES Person.msg)
    generate_messages(DEPENDENCIES std_msgs)
    catkin_package( ... message_runtime)
    ```

示例代码见[古月ROS入门21讲](https://github.com/huchunxu/ros_21_tutorials/tree/master/learning_topic)

#### Service

.srv文件分为两部分, 分别是request和response, 在srv文件中以 --- 分隔

1. 定义srv文件

    ```msg
    string name
    uint8 sex
    uint8 age

    uint8 unknown = 0
    uint8 male = 1
    uint8 female = 2
    ---
    string result
    ```

1. 在package.xml中添加功能包依赖

    ```xml
    <build_depend>message_generation</build_depend>
    <exec_depend>message_runtime</exec_depend>
    ```

1. 在CmakeLists.txt中添加编译选项

    ```cmake
    find_package( ... message_generation)
    add_service_files(FILES Person.srv)
    generate_messages(DEPENDENCIES std_msgs)
    catkin_package( ... message_runtime)
    ```

示例代码见[古月ROS入门21讲](https://github.com/huchunxu/ros_21_tutorials/tree/master/learning_service)

#### Parameter

主要就是对set函数和get函数的使用
C++版本

```cpp
//将name="/param"的参数设置为0
ros::param::set("/param", 0);
//将name="/param"的参数读取到param
ros::param::get("/param", param);
```

Python版本

```python
# 将name="/param"的参数设置为0
rospy.set_param("/param", 0)
# 将name="/param"的参数读取到param
param = rospy.get_param('/param')
```

示例代码见[古月ROS入门21讲](https://github.com/huchunxu/ros_21_tutorials/tree/master/learning_parameter)

#### Launch

在ros系统中, 可以使用rosrun启动Node, 但是很多时候一整个系统的Node数量非常多, 这个时候就需要roslaunch运行.launch文件来代替

```xml
<launch><!--根元素-->
  <!-- pkg是Node的可执行文件所在的包, type是可执行文件的名字, name是Node名称, 同一个可执行文件可以创建多个Node, 但这些Node不可有相同的名称 -->
  <node pkg="package-name" type="executable-name" name="node-name"></node>
  <!-- 键值对 -->
  <param name="name" value="value" />
  <!-- 从文件读取参数 -->
  <rosparam file="params.yml" command="load" />
  <!-- 重映射资源, 一般是主题 -->
  <remap from="/topic" to="/another_topic" />
</launch>
```

<!-- TODO:insert my mrcnn-ros -->