---
layout: post
title: cuda编程的基础概念总结
date: 2021-03-14 10:07:24.000000000 +09:00
img:  one-piece/one-piece36.jpg # Add image post (optional)
tag: [c与c++]
---



# GPU的基础介绍

首先上一张显卡主板构造图。

![dfs]({{site.baseurl}}/assets/img/cuda/00.png)

其中GPU芯片是重点，其结构图如下，它一般有很多核心，这些核的学术名字叫流多处理器（stream multiprocessor，SM），每个SM根据GPU型号的不同由若干个流处理器（stream processor，SP）组成，SP的功能只有计算。



<div style="text-align: center">
<img src="{{site.baseurl}}/assets/img/cuda/04.png"   width="840" height="420"/>
</div>

# cuda编程的基本概念



## 主机端host与设备端device

> 一个工程，我们想要有的代码在cpu上执行，有的代码在gpu上执行。因此就需要有host与device的概念。

GPU是完全不同于CPU的处理器，为了方便地同时为CPU和GPU编写代码，处理好两种处理器间各自计算、内存、数据传递等任务，CUDA使用C语言作为CPU端的开发环境，并在GPU端使用几乎与CPU端一致的语法进行开发，只是使用一些特定的关键字来区分CPU端和GPU端的代码。

CUDA中，为了更好的区分CPU和GPU，将CPU作为主机端（Host），GPU作为设备端（Device），每个进程（process）中，通过一个Host控制一个或多个Device协同工作。通过这种方式，CPU负责进行逻辑性强的事务性处理和串行计算，而GPU则专注于执行高度线程化的并行处理任务。

通过这种方式，首先实现了CUDA中最上层的并行：Host与Device的并行。也可以理解为将计算任务分解成若干子任务分别交给CPU和GPU。如下图所示，Host和Device有各自的流水线，计算和内存等均彼此独立，不过Device依然由Host控制，控制Device的工作也是Host流水线上的一部分。

虽然在大多数时候，为了结果的正确性需要经常在必要处进行Host与Device同步（往往是CPU、GPU间的数据通信），此时CPU或GPU会空等另一边执行到同步出再继续计算。但在此基础上进行进一步优化往往可以带来很好的优化效果，比如采用通信隐藏、或者插入无关计算等方法。

![dfs]({{site.baseurl}}/assets/img/cuda/01.png)



>  在正常c语言中，设备端函数用`__global__`来修饰。在执行时需要使用特定的调用格式。

## 设备端代码的执行

> 一般从主机代码中启动设备代码叫做**内核调用**。内核调用有固定的语法`func << <block数量，每个block中线程数量，共享内存大小  >> >(参数列表)`

CUDA中，运行在GPU上的并行计算函数称为kernel函数。

**内核调用是以线程网格（grid）的形式组织的，每个grid由若干个线程块（block）组成，而每个block又由若干个线程（thread）组成**。block的数量只要小于$2^{31}-1$就好，thread的数量现在一般都是1024了。

![dfs]({{site.baseurl}}/assets/img/cuda/02.png)



## cuda编程时的内存结构

![dfs]({{site.baseurl}}/assets/img/cuda/03.png)



> 除了表格中的，其实还有L1和L2缓存。其中L1缓存一般是每个流多处理器（gpu的核）都有的，一般与shared memory使用相同的存储硬件，L2缓存是整个gpu中所有的流多处理器共享的，所有的global memory和local memory都使用这些缓存。同时，所有的global memory访问通过L2缓存进行。



在`__global__`修饰的设备函数中声明的局部变量肯定使用的是local memory或者是register（一开始会优先使用register，如果register装不下，就会使用local memory）；而使用`cudaMalloc`分配的内存都是global memory



此处说的global memory对应的物理结构也就是本文开头的显卡主板构造中的显存芯片，这是在gpu芯片之外的，因此访问速度会比较慢。



## `.cu`程序编译

带有cuda编程的源文件一般是`.cu`结尾的，这种源文件的编译使用`nvcc`编译器，使用方法与`gcc`一致。