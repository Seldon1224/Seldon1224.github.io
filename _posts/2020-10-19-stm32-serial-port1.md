---
title: stm32学习-串口乱码
categories: [stm32, Debug]
tags: ['stm32','串口']
---

# stm32-hal库串口乱码解决办法

在做openmv与stm32通信时，一直出现乱码问题，接收到的信息一直为乱码，无法进行处理。

经查询资料，解决方法如下：

* 首先确认我们的晶振是否正常使用

* 在检查好外设没有问退后，打开我们的工程文件，选择“stm32xxxxxx.h”，如图所示

  ![](https://exp-picture.cdn.bcebos.com/2db6c1b2dc19ce2c6409586a7fdca039121f11aa.jpg?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_500%2Climit_1)

* 到文件中有“HSE_VALUE”选项，查看是否和我们的晶振频率一致

* 若在此文件未找到，可在HAL库文件下的“stm32xxxx_hal_conf.h”文件找到“HSE_VALUE”

  ![](https://exp-picture.cdn.bcebos.com/d2987775f2c4ec99105142b5c3fe1e425c6b07aa.jpg?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_500%2Climit_1)



* 修改HSE_VALUE大小为stm32上的晶振大小（之前我的数值为12000000U，经修改后串口无乱码）
  ![0vanC6.png](https://s1.ax1x.com/2020/10/19/0vanC6.png)