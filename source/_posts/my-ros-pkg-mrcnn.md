---
title: 我开发的mrcnn的ros包
date: 2020-04-03 23:01:23
tags:
---
# 我的Mask-R-CNN的ROS包

主要工作是将[matterport/Mask_RCNN](https://github.com/matterport/Mask_RCNN)套入到ROS的框架中

## 功能介绍

### Parameters

- ~coco_path: string
    coco文件夹的地址, 默认是包文件夹下的scripts/coco/
- ~coco_model_path: string
    从coco数据集训练出来的权重, 默认是包文件夹下的mask_rcnn_coco.h5

## Topics Published

- /mrcnn_image: sensor_msgs/Image
    经过mask-r-cnn识别之后的图片可视化
- /mrcnn_result: mask_rcnn/mrcnn_result
    自定义的数据格式, 详情见mrcnn_result.msg, 是经过mask-r-cnn识别之后的结果, 包括rois, class_ids, class_names, scores

## Topics Subscribed

- /usb_cam/image_raw: sensor_msgs/Image
    usb_cam包的发布的Topic

## 改造过程

### 生成图片

在matterport/Mask_RCNN的代码中, 图片是直接通过plt.show()显示的, 并没有将图片保存到变量中, 所以我需要将图片保存到变量中. 参考了[官方教程](https://matplotlib.org/gallery/misc/agg_buffer_to_array.html)和一个[stackoverflow](https://stackoverflow.com/questions/55261576/plt-plot-to-opencv-image-numpy-array), 最终的代码在my_visualize.py中, 如下

```python
from matplotlib.figure import Figure
from matplotlib.backends.backend_agg import FigureCanvasAgg
############################
# somethings
############################
fig = Figure()
canvas = FigureCanvasAgg(fig)
ax = fig.gca()
###########################
# 使用上面创建的ax, 用matplotlib进行绘图
###########################
canvas.draw()
result = np.fromstring(canvas.tostring_rgb(), dtype='uint8')
result = result.reshape(height, width, 3)
return result
```

### 使用cv_bridge

在ROS中使用的图片的数据格式是sensor_msgs/Image, 但是在opencv中使用的是numpy.ndarray, 所以两者需要转换, 转换的方式是使用cv_bridge

{% asset_img cvbridge.png 'cvbridge' %}

使用方法很简单, 网上可以找到很多教程, 或者看我的代码.
!但是, 一开始使用的时候会报错

```shell
ImportError: dynamic module does not define module export function (PyInit_cv_bridge_boost)
```

产生该报错的原因是因为ROS自己编译的cv_bridge是python2版本, 我是使用python3, 版本不兼容, 所以报错.
[解决方法](https://medium.com/@beta_b0t/how-to-setup-ros-with-python-3-44a69ca36674)是下载cv_bridage的c++源码, 自己编译并覆盖原本的cv_bridge, 一个细节是, 如果是使用Anaconda, 那么要先将环境deactivate, 另一个细节是在source install/setup.bash时要加参数--extend, 不然这次的setup.bash 会覆盖上一次

```shell
sudo apt-get install python-catkin-tools python3-dev python3-numpy

mkdir ~/catkin_build_ws && cd ~/catkin_build_ws
catkin config -DPYTHON_EXECUTABLE=/usr/bin/python3 -DPYTHON_INCLUDE_DIR=/usr/include/python3.6m -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.6m.so
catkin config --install

mkdir src
cd src
git clone -b melodic git@github.com:ros-perception/vision_opencv.git

cd ~/catkin_build_ws
catkin build cv_bridge
source install/setup.bash --extend
```

### 自定义.msg文件

ROS支持自定义消息的数据格式, 所以我自己也定义了一个mrcnn_result数据格式, , 具体步骤可以看{% post_link learn-slam '另一篇博客'%}但是, 如果自定义数据格式里包含其他的数据结构, 比如我自定义的这个mrcnn_result, ROS就会报错

```shell
ImportError: No module named 'em'
```

即使安装了empy也没有效果, 貌似是只能用python2编译的感觉, 所以我新建了一个工作空间(原来的工作空间是用python3的, 会和python2产生冲突), 把代码放到里面, 再编译, 果然成功了, 之后再用python3引用这个工作空间中的数据格式就好

## 运行

运行过程中的数据流图如图

{% asset_img rosgraph.png 'rosgraph' %}
