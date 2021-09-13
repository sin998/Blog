[TOC]

# 【GAMES101-现代计算机图形学入门-闫令琪】笔记 Lecture 01 Overview of Computer Graphics

# 参考资料&原文链接

[GAMES101-现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744)

# 导师介绍

![image-20210912152830009](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121528303.png)

![image-20210912152849308](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121528550.png)

![image-20210912152923303](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121529058.png)

# 今日目标

![image-20210912153057872](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121530319.png)

# 图形学可以做什么

## 游戏

一般来说判断游戏画面好坏的第一反应是看游戏画面是否够亮，如果够亮的话则代表全局光照好，否则就突出了一些画质上的技术问题没有解决。

《只狼》

![image-20210912153257703](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121532216.png)

那么漫画风格的游戏是如何做出来的呢，怎么做才能做出这种风格呢。

《无主之地3》

![image-20210912153422065](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121534653.png)

矩阵最简单的应用就是特效。虽然我也不清楚为什么The Matrix会被翻译成黑客帝国。

## 电影

《黑客帝国》

![image-20210912153512079](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121535370.png)

面部捕捉。

《阿凡达》

![image-20210912153646695](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121536164.png)

## 动画

动画，动物的毛发和毛发的动作，与光线的作用。

《疯狂动物城》

![image-20210912153731664](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121537067.png)

模拟粒子运动、布料、头发运动。

《冰雪奇缘2》

![image-20210912153823086](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121538304.png)

## 设计

奔驰的概念设计（左）。

![image-20210912153902138](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121539691.png)

家装设计。

![image-20210912154944732](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121549797.png)

## 可视化

人体、数据可视化。

![image-20210912155032466](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121550739.png)

## 虚拟现实

VR，你带上那个头显，看到的东西全是电脑生成的。而AR是增强现实，是在真实世界里面叠加了虚拟的东西。

![image-20210912155119459](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121551765.png)

## 数码插图

用电脑和其他设备来画图。

![image-20210912155254820](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121552973.png)

## 模拟

模拟沙子、黑洞的行为。

![image-20210912155732211](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121557614.png)

## 图形用户界面

用户界面设计和交互。

![image-20210912155824208](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121558471.png)

## 字体设计

矢量图不会模糊。

![image-20210912155845120](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121558789.png)

# 为什么要学习计算机图形学

![image-20210912160016801](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121600697.png)

![image-20210912160045798](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121600648.png)

![image-20210912160105832](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121601585.png)

# 计算机图形学是什么

![image-20210912160146278](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121601199.png)

## 光栅化

![image-20210912160203557](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121602980.png)

游戏画面达到30FPS就叫做实时。

## 几何

![image-20210912160254505](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121602536.png)

## 光线追踪

![image-20210912160322376](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121603718.png)

## 动画和仿真

右边是个动图，建议看原视频。

![image-20210912160400167](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121604342.png)

# 这门课不学什么

OpenGL等软件的使用。

![image-20210912160457626](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121604898.png)

3D模型软件的使用。

![image-20210912160602615](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121606963.png)

计算机视觉。

![image-20210912160611166](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121606948.png)

计算机图形学与计算机视觉的不同之处。

![image-20210912160731464](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121607304.png)

# 其他信息

![image-20210912160858506](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121608662.png)

![image-20210912160908946](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121609662.png)

## 推荐书籍

虎书

![image-20210912160945460](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121609285.png)

## Q&A

![image-20210912161003291](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121610174.png)

## 作业

![image-20210912161104891](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121611607.png)

![image-20210912161119457](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121611427.png)

## 使用IDE

![image-20210912161148495](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121611616.png)

## 学术诚信

![image-20210912161210445](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109121612171.png)

# 本文标签

`计算机图形学`、`游戏开发`、`GAMES101-现代计算机图形学入门-闫令琪`、`Unreal Engine`、`游戏开发基础`、`视频系列笔记`、`笔记`。

