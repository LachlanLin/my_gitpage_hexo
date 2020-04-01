---
layout: post
title: "How to use fitcsvm"
date: 2019/2/9 13:01
updated: 2019/2/10 15:20
tags:
- matlab
- machine learning
---
# fitcsvm介绍

[官网链接](https://ww2.mathworks.cn/help/releases/R2017a/stats/fitcsvm.html)
我的matlab版本为2017a, 但是应该2018a和2018b都可以适用本文.

{% asset_img p1.png img_1 %}

fitcsvm归属matlab的统计与机器学习工具箱中的类CompactClassificationSVM

## 参数介绍

### BoxConstraint

{% asset_img p2.png img_2 %}

一开始不明白是什么意思, 又去查了官网论坛,
得到一个[回答](https://ww2.mathworks.cn/matlabcentral/answers/301213-what-is-box-constraint-in-svmtrain-fucntion)

{% asset_img p3.png img_3 %}

可以看出BoxConstraint的基本思想和惩罚因子C相同, 所以, BoxConstraint应该与C有一定的联系(个人猜测这就是惩罚因子C)

### KernelFunction

{% asset_img p4.png img_4 %}

可以从字面看出, 这就是核函数的参数, 一共有三种可选参数, 高斯, 线性, 多项式.

### KernelScale

{% asset_img p5.png img_5 %}

这里说软件将预测器矩阵X的所有元素除以值KernelScale。然后，软件应用适当的内核规范来计算Gram矩阵。
所以, 应该是所有的数据都会除以这个标量, 然后再计算核函数, 利用这个, 可以用来改变rbf的参数sigma. 经计算, $KernelScale = \sqrt{2}*sigma$，所以, 可以通过改变KernelScale改变sigma.

### PolynomialOrder

{% asset_img p6.png img_6 %}

在用多项式核函数时可以调节多项式参数p(p的位置见KernelFunction词条)

### OptimizeHyperparameters

{% asset_img p7.png img_7 %}

这是我所写的参数里面最复杂的一个, 默认是'none'不要优化, 第二种,使用'auto'也就是相当于优化 {'BoxConstraint', 'KernelScale'},
可以自己调节要优化的参数, 当然, 要优化的参数越多, 运行时间越久, 我使用2个参数优化, 一个分类器跑了1分钟还多.

### 'HyperparameterOptimizationOptions'

这是超参数优化的设置, 这里只介绍一个参数'ShowPlots', 在超参数优化时会出来一幅图, 严重妨碍视野, 拖慢cpu处理速度. 利用此参数可以关掉画图.

## 预测

### predict

与fitcsvm同属于类CompactClassificationSVM, 用于为fitcsvm得到的分类器模型进行分类
[官网链接](https://ww2.mathworks.cn/help/releases/R2017a/stats/compactclassificationsvm.predict.html)
应用predict得到的第一个输出量就是分类器的分类结果标签, 也就是类别标签.

{% asset_img p8.png img_8 %}

第二个输出量可以简单理解为: 该测试点为所预测的类别标签(即第一个返回值)的可能性, 当该值为正数时, 判为正类, 为负数时, 判为负类.

## 代码示例

接下来, 介绍了参数之后, 我希望读者可以去[原网站](https://ww2.mathworks.cn/help/releases/R2017a/stats/fitcsvm.html?)看看详细解释, 多读读官方文档还是很好的.
知道了上面所有的参数的功能之后, 开始演示代码编写(以一个简单的男女分类问题为例, 使用rbf核函数).

```matlab
SVMModel_rbf = fitcsvm(source_train,label_train,'KernelFunction','rbf','OptimizeHyperparameters',{'BoxConstraint','KernelScale'},  'HyperparameterOptimizationOptions',struct('ShowPlots',false));
[ans_test_male,~]=predict(SVMModel_rbf,test_male);
[ans_test_female,~]=predict(SVMModel_rbfalall;
```

source_train是我的数据集, 包括男和女, label_train是数据集的类别标签,ans_test_male是分类完得到的类别标签, 我用1代表男, 0代表女. 由于第二个predict的返回值暂时不会用到, 所以不保存, 使用~.
fitcsvm的参数使用键值对表示

- 'KernelFunction','rbf' 表示核函数采用rbf
- 'OptimizeHyperparameters',{'BoxConstraint','KernelScale'} 表示对'BoxConstraint','KernelScale'进行超参数优化,
- 'HyperparameterOptimizationOptions',struct('ShowPlots',false)表示关掉画超参数优化的图, 你可以试试不加这一句, 结果应该是会出来一个没什么用的图.
- predict是应用分类器进行分类, 第一个参数是训练完成的分类器, 第二个参数是待分类器的测试集.
