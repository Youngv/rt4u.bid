---
title: 我的智能小车（一）——开场篇
---
小时候能拥有一辆遥控车便是我的梦想，而现在能够制作出一辆遥控车成了我的梦想，看着一个个小零件在我的手中逐渐成为了小车的一部分，而我DIY的智能小车也逐渐有了基本的功能，我油然而生一种不折腾会死星人的自豪感，下面就让我一一介绍我的智能小车的各个功能的实现过程，但是在这之前，作为**我的智能小车系列**的开场篇，我先要介绍一下我的智能小车的各个部件及计算机开发环境基本配置。

**小车底板及电机：**

![](https://f002.backblazeb2.com/file/as-cdn/blog/%E5%B0%8F%E8%BD%A6%E5%BA%95%E6%9D%BF%E5%8F%8A%E7%94%B5%E6%9C%BA.jpg)

由两个电机的转速差实现小车的移动方向，后面的万向轮可以自由转动。

**L298N双电机驱动模块：**

![](https://f002.backblazeb2.com/file/as-cdn/blog/L298N%E5%8F%8C%E7%94%B5%E6%9C%BA%E9%A9%B1%E5%8A%A8%E6%A8%A1%E5%9D%97.jpg)

**对射式测速传感器模块：**

![](https://f002.backblazeb2.com/file/as-cdn/blog/%E5%AF%B9%E5%B0%84%E5%BC%8F%E6%B5%8B%E9%80%9F%E4%BC%A0%E6%84%9F%E5%99%A8%E6%A8%A1%E5%9D%97.jpg)

**HMC5883L电子指南针模块：**

![](https://f002.backblazeb2.com/file/as-cdn/blog/HMC5883L%E7%94%B5%E5%AD%90%E6%8C%87%E5%8D%97%E9%92%88%E6%A8%A1%E5%9D%97.jpg)

**Arduino Uno R3 Plus 开发板：**

![](https://f002.backblazeb2.com/file/as-cdn/blog/Arduino%20Uno%20R3%20Plus%20%E5%BC%80%E5%8F%91%E6%9D%BF.jpg)

**蓝牙从机模块：**

![](https://f002.backblazeb2.com/file/as-cdn/blog/%E8%93%9D%E7%89%99%E4%BB%8E%E6%9C%BA%E6%A8%A1%E5%9D%97.jpg)

**Wireless SD Shield,XBEE/蓝牙/舵机口/传感器/APC2XX一体扩展板**

![](https://f002.backblazeb2.com/file/as-cdn/blog/%E4%B8%80%E4%BD%93%E6%89%A9%E5%B1%95%E6%9D%BF.jpg)

**8V 3500MAh锂电池组**

![](https://f002.backblazeb2.com/file/as-cdn/blog/%E9%94%82%E7%94%B5%E6%B1%A0%E7%BB%84.jpg)

**计算机开发环境：**

*   系统：Ubuntu 12.04 LTS Desktop 32位

*   Arduino 开发软件：Arduino 集成开发环境

*   蓝牙设置软件：Blueman蓝牙管理器

*   串口调试软件：CuteCom

*   App Inventor 开发环境

上面的软件都能在 Ubuntu——系统工具——Ubuntu软件中心 搜索安装，而App Inventor 开发环境配置属于Android 软件的开发，如果你感兴趣可以试试搜索有关教程。