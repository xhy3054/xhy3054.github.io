---
layout: post
title: 带环链表问题
date: 2019-04-02 10:07:24.000000000 +09:00
img:  sword/sword4.jpg # Add image post (optional)
tag: [算法与数据结构]
---

昨天去面试，面试官问了我一道带环的链表问题。因为以前一个师姐问过我这个问题，我在网上看到过正确解法，因此很轻松的就写出了解法。但是当面试官让我将这个问题用公式推导出来时，，很尴尬，我忽然脑子僵了，多亏面试官提醒了一下，我才推导出来。

> 事后回想，总结如下: **任何问题不能只记住解法，而要真正将这个问题弄清楚。**

# 链表结构描述
其实就是一个6字型的链表问题，一般我们解这样的题，都是设置两个前进节点，一个叫fast，每次走两步，一个叫slow，每次走一步:
1. 假设链表入口到环的入口长度为l1;

2. 假设环的入口到第一次相遇点处长度为l2;

3. 假设环的长度为s;

## 问题一: 判断链表有没有环
首先明确一点事实：**只有有环，fast与slow总会相遇**。

- 当相遇时，slow走了`l1+l2`，fast走了`2(l1+l2)`，并且fast比slow多走了n个环的长度；
    - 即`l1+l2 = ns`

> 由上面推导可知，l2从0开始，每次加1，直到`l1+l2`是s的整数倍数时，slow与fast第一次相遇。

## 问题二: 判断链表有没有环，如果有，返回环的长度
在问题一中已经将判断链表有没有环的问题搞清楚了，下面怎么得到环的长度呢？

- 在fast与slow第一次相遇后，让二者继续前进，当再次相遇时，slow在这段间隔中走的步数就是环的长度。

## 问题三: 判断链表有没有环，如果有，返回环入口节点
在问题一中已经将判断链表有没有环的问题搞清楚了，下面怎么得到环的入口节点呢？

- 在fast与slow第一次相遇时，从新定义一个前进节点slow2从头出发，slow从当前位置出发，则slow与slow2相遇的节点就是环的入口节点。
    - `l1+l2+l1 = ns + l1`


# 总结
以上就是对有环链表的三个思考了，写下来希望自己忘记了可以随时翻看。
