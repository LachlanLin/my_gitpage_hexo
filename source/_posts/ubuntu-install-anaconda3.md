---
title: "Ubuntu 18.04 安装 Anaconda3"
date: 2019/8/28 22:31:57
tags:
---
### 下载安装包

[官网](https://www.anaconda.com)

### 安装

```bash
sudo sh Anaconda3-2019.07-Linux-x86_64.sh
```

会出现许可证, 一直按回车键就好, 最后会出现安装目录, 我选择默认安装目录

{% asset_img p1.png img_1 %}

之后还会出现询问是否初始化, 我选择了"是"
安装完成

{% asset_img p2.png img_2 %}

### 出现问题

命令行无法使用conda
[参考](https://blog.csdn.net/sinat_34520704/article/details/86645111)

```bash
sudo vi ~/.bashrc
```

在最后加入export PATH="/home/lachlan/anaconda3/bin:$PATH"

```bash
source ~/.bashrc
```

重启另一个终端, 可以使用conda命令了
但是终端中一直有一个(base)前缀, 查找到[解决方案](https://blog.csdn.net/devcy/article/details/100099068)
关闭自动启动base之后, 以后要进入conda环境
使用

```bash
conda activate <environmentName>
```

所以, 开启base环境的代码是

```bash
conda activate base
```

conda命令无法进行升级和创建环境操作, 提示权限不够, 所以给anaconda3文件夹加权限

```bash
chmod -R 777 anaconda3
```
