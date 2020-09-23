---
title: CubeMX生成keil项目芯片为??device??(STMicroelectronics)的解决方法
categories: [Stm32CubeMX, 软件使用]
tags: ['问题解决']
---

# CubeMX生成keil项目芯片为??device??(STMicroelectronics)的解决方法

之前手贱安装了Jdk13，无意中导致了CubeMX代码无法正常生成，最后在网上找到解决办法：<https://blog.csdn.net/na2wo4/article/details/104257432>

CubeMX软件运行需要jre的环境，如果电脑中存在Jdk13，CubeMX会出现如下问题：

>  打开工程后会提示??device??(STMicroelectronics)的warning

[![wvrzc9.png](https://s1.ax1x.com/2020/09/23/wvrzc9.png)](https://imgchr.com/i/wvrzc9)

同时CubeMX生成代码后会有the code is sucessfully generated under but but MDK-ARM 5.27 project generation have a problem的报错。

## 解决办法：

* 电脑中不要同时有两个版本的jdk或者jre，目前stm32CubeMX只支持Jdk8，不支持更高版本。
* 在删除jdk13后，软件使用恢复正常。

[![wvyRzQ.png](https://s1.ax1x.com/2020/09/23/wvyRzQ.png)](https://imgchr.com/i/wvyRzQ)