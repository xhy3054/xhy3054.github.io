---
layout: post
title: 镜头的参数与成像
date: 2021-01-17 10:07:24.000000000 +09:00
img:  one-piece/one-piece32.jpg # Add image post (optional)
tag: [计算机视觉, 多传感器融合]
---

# 镜头的参数与成像

> 最近几天手头刚好有一个闲置的变焦镜头，在把玩的同时对相机的成像特点又有了一些理解。为了不让这些理解被丢进记忆的垃圾堆，打算做一些记录和梳理。

## 镜头
镜头的参数其实是很多的，主要有

- 焦距：可以分为物理焦距与摄影焦距。
- 光圈：大小决定了图像的亮度，在拍摄高斯运动物体、曝光时间很短的应用中，应该选用大光圈镜头，以提高亮度。
- 景深：指的是在理想对焦状态下物体允许的前后方向的移动量。（在此范围内可以认为成像是清晰地）
- 视野范围：比较简单的理解就是有多少度（手头这个最大110°）的视场；
- 对应最大ccd尺寸：镜头成像直径可以覆盖的最大CCD芯片的尺寸；
- 接口：比如C、CS等，只要可以和相机接口匹配安装即可；

### 焦距

- 物理焦距：一个单片凸透镜的焦距指的是**平行光线经过凸透镜汇聚后的一点到透镜中心的距离（如下图所示）**。一般来说，物理焦距在镜片制作完成后就固定了，是不可变的性质。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/phsic_fl.png" />
</div>

当然，现在很多镜头其实是**变焦镜头**，这些镜头一般由多片透镜组成，可以通过调节多个透镜之间的相对距离，来对整体的物理焦距进行调整。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/change_fl.png"   width="750" height="450"/>
</div>

- 摄影焦距：相信很多人都有对着单片的定焦镜头进行旋转的调焦经历，其实此处调节的不是物理焦距，而是**摄影焦距---即相距，成像平面（cmos芯片）与镜头之间的距离**。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/distance.jpeg"   width="750" height="450"/>
</div>

通过对相距的调节，使得其与焦距匹配，可以获得清晰的成像。

### 调焦的原因

谈到调焦的原因，首先需要谈到凸透镜的两个光学性质。
- 同一个三维点发出的各个方向的光线，经过镜头后必定汇聚于同一点；
- 与透镜切面平行的平面上不同的点发出的光线，经过镜头后，汇聚于不同的点；

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/image.png"   width="750" height="450"/>
</div>

上述的两个性质使得：对于与透镜切面平行的一个面，通过调整相距，可以通过透镜清晰成像。如果摄影焦距不当，则会导致一个点不同方向的光线会投影到成像平面上的不同点，出现混叠。

### 焦距与距离的关系

有的时候，我们使用相机拍摄经常会拍摄出如下的照片，只有某个距离的成像是清晰地，更近或者更远的场景成像是模糊的。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/sence_depth1.png"   width="750" height="250"/>
</div>

这个现象的原因是，**对于距离镜头切面不同距离的点，摄影焦距是不一样的。**如下图所示，A1与A2的摄影焦距显然不同。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/distance2.png"   width="750" height="350"/>
</div>

因此，根据我们想要拍摄的物体到镜头的距离，我们需要调出一个合适的相距，才能使得该物体在cmos芯片上得到清晰的成像。

### 景深

看到这里，很多人陷入了疑惑：之前我看到的那些清晰的照片都是怎么来的呢？这就要提及景深的概念了。

事实上，人眼的分辨能力是有限的，因此即使相距与摄影焦距不是完全相同，只要在某个阈值以内，我们看到的成像都是清晰地。而这就带来了景深的概念。**景深指的是某个距离范围**，在这个范围内成的像都可以看作是清晰的。如下图所示。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/sence_depth.png"   width="750" height="300"/>
</div>

可以想象，如果景深很大的话，随便拍一张图片，也基本都是清楚的。一般来说，景深随着镜头的光圈值、焦距、拍摄距离而变化。

- 光圈越小，景深越大；
- 物理焦距越小，景深越大；（后两个需要测试确定）
- 摄影焦距越大，景深越大；

### 光圈
镜头的光圈影响了进光量，也影响了景深。一般光圈的大小可以用f值来表示，表示开了几分之几。比如说f/2.0表示开了1/2，如下：

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/optical.png"   width="750" height="200"/>
</div>

一般来说，光圈越大，代表进光量越大，那么曝光时间相应的就可以设置的短一点，比较适合用来拍摄快速运动的物体。

同时，光圈越大，会使得景深变小（如下图），因此也不能无限扩大光圈。

<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/optical_depth.png"   width="750" height="400"/>
</div>

### 镜头的选择
- 接口：需要和相机接口匹配（比如当前手头的就是C接口的）；
- CCD尺寸：镜头可支持的最大CCD尺寸应大于选配相机的CCD尺寸（不然最后图像四周会出现黑边）；
- 镜头焦距：可以根据相机CCD尺寸(相机型号尺寸)、工作距离、视场大小（也就是物体的高宽等）计算所需的镜头焦距；
<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/lens/calcu.png"   width="550" height="200"/>
</div>
- 镜头光圈范围：根据所需曝光时间、景深等联合决定；


# 参考文献
- [1] https://www.cnblogs.com/YongQiVisionIMAX/p/13872075.html