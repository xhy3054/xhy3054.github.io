---
layout: post
title: 特征点法slam中位姿估计方法总结 
date: 2019-04-21 10:07:24.000000000 +09:00
img:  sword/sword9.jpg # Add image post (optional)
tag: [计算机视觉]
---

这篇博客主要是对基于特征点匹配的slam中的位姿估计方法进行总结。

## 2d-2d的位姿估计
一般用在单目slam的初始阶段，系统还没有地图，此时只有输入的2d的图片序列。一般会使用如下方法进行位姿估计。而RGBD与双目系统中，无需进行初始化。

1. 首先通过对极约束求解出本质矩阵(比较经典的有八点法)，然后对本质矩阵进行奇异值分解(SVD)，求解出R与t;
    - 原理公式$ E = t^{\land}R $([对极约束](https://xhy3054.github.io/epipolar-geometry/))

2. 通过单应约束求解出单应矩阵，然后通过对单应矩阵进行分解求解出R与t;
    - 原理公式$ H = K(R + t \cdot -\frac{n^{T}}{d}) K^{-1} $ ([单应约束](https://xhy3054.github.io/Homography-matrix/))

> 这两种方法有一个共同的缺点，那就是尺度不确定性。所以，在单目slam中，尺度是不确定的，通常，我们会对初始化时的平移向量t进行归一化，此后，建立的地图与整个之后的位姿都是以初始化时的位移作为单位进行的。**这个特性导致特征点法的单目vo是尺度不确定的**。在初始化之后，单目相机就会使用下面的3d-2d方法进行位姿估计

> 此处再讨论一下为什么对极约束没有单应约束强，还有必须有位移等缺点，还是首先使用对极约束进行初始化。这时因为单应约束有平面场景要求，使得初始化时必须有平面场景。

## 3d-2d的位姿估计（PnP问题）
在slam正常运行起来之后，会同时建立起基于离散点的地图。在这个时候，当新读入一张图片时，我们可以直接将地图点与新图中的特征点进行匹配。此时通过3d-2d的位姿估计会比2d-2d的方法稳定，方便很多。通常我们将3d-2d的位姿估计问题叫做**PnP问题**。PnP问题有很多求解方法，比如

1. 直接线性变换(DLT, Direct Linear Transformation): 最少需要6对匹配点

2. P3P(perspective three point): 最少需要3对点即可

3. EPnP(Efficient PnP: an accurate o(n) solution to the pnp problem)

4. UPnP等线性方法

5. Bundle Adjustment(最小化重投影误差)等非线性优化方法

> 这些方法在opencv中基本都有函数接口提供

## 3d-3d的位姿估计
3d-3d的位姿估计指的是我们拥有一组配对好的3d点，然后根据其恢复出位姿，这个问题一般不会很强调是视觉的slam了，因为一组配对好的3d点与相机模型基本没有太大的关系了,在激光slam中也有可能会碰到ICP，不过由于激光数据特征不够丰富，因此我们很难知道两个点集之间的匹配关系。

一般这种问题会使用迭代最近点(Iterative Closest Point, ICP)思想，求解方法主要分为两种方式:
1. 基于线性代数的求解
    - SVD

2. 基于非线性优化方式的求解
    - 确定目标函数，并进行非线性优化

> 这种位姿估计的场景其实在视觉slam中一般用来估计与校正回环的累积误差，计算能使回环对齐的相似变换。不过在RGBD与双目相机系统中，也可以使用这种方法来计算位姿。

## 代码
> [2d-2d](https://github.com/xhy3054/myslam/tree/master/04-VO-feature/pose_estimation_2d2d)代码、[3d-2d](https://github.com/xhy3054/myslam/tree/master/04-VO-feature/pose_estimation_3d2d)代码、[3d-3d](https://github.com/xhy3054/myslam/tree/master/04-VO-feature/pose_estimation_3d3d)代码

# 参考文献
- [1] 视觉SLAM十四讲， 高翔，张涛等著

