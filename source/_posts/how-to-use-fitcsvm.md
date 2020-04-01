---
layout: post
title: "How to use fitcsvm"
date: 2019/2/9 13:01
---
# 超参数优化

## OptimizeHyperparameters
![图7](https://img-blog.csdnimg.cn/20190613122009162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xhY2hsYW5fX0w=,size_16,color_FFFFFF,t_70)
这是我所写的参数里面最复杂的一个, 默认是'none'不要优化, 第二种,使用'auto'也就是相当于优化 {'BoxConstraint', 'KernelScale'},
可以自己调节要优化的参数, 当然, 要优化的参数越多, 运行时间越久, 我使用2个参数优化, 一个分类器跑了1分钟还多.

## 'HyperparameterOptimizationOptions'
这是超参数优化的设置, 这里只介绍一个参数'ShowPlots', 在超参数优化时会出来一幅图, 严重妨碍视野, 拖慢cpu处理速度. 利用此参数可以关掉画图.

# predict
与fitcsvm同属于类CompactClassificationSVM, 用于为fitcsvm得到的分类器模型进行分类
[官网链接](https://ww2.mathworks.cn/help/releases/R2017a/stats/compactclassificationsvm.predict.html)
应用predict得到的第一个输出量就是分类器的分类结果标签, 也就是类别标签.
![图](https://img-blog.csdnimg.cn/20190613133211908.png)
第二个输出量可以简单理解为: 该测试点为所预测的类别标签(即第一个返回值)的可能性, 当该值为正数时, 判为正类, 为负数时, 判为负类.


# 代码示例
接下来, 介绍了参数之后, 我希望读者可以去[原网站](https://ww2.mathworks.cn/help/releases/R2017a/stats/fitcsvm.html?)看看详细解释, 多读读官方文档还是很好的.
知道了上面所有的参数的功能之后, 开始演示代码编写(以一个简单的男女分类问题为例, 使用rbf核函数).
```matlab
SVMModel_rbf = fitcsvm(source_train,label_train,'KernelFunction','rbf','OptimizeHyperparameters',{'BoxConstraint','KernelScale'},  'HyperparameterOptimizationOptions',struct('ShowPlots',false));
[ans_test_male,~]=predict(SVMModel_rbf,test_male);
[ans_test_female,~]=predict(SVMModel_rbfalall;
```
source_train是我的数据集, 包括男和女, label_train是数据集的类别标签,ans_test_male是分类完得到的类别标签, 我用1代表男, 0代表女. 由于第二个predict的返回值暂时不会用到, 所以不保存, 使用~.
fitcsvm的参数使用键值对表示, 
- 'KernelFunction','rbf' 表示核函数采用rbf
- 'OptimizeHyperparameters',{'BoxConstraint','KernelScale'} 表示对'BoxConstraint','KernelScale'进行超参数优化,
-  'HyperparameterOptimizationOptions',struct('ShowPlots',false)表示关掉画超参数优化的图, 你可以试试不加这一句, 结果应该是会出来一个没什么用的图.
- predict是应用分类器进行分类, 第一个参数是训练完成的分类器, 第二个参数是待分类器的测试集.
