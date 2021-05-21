---
title: 我的智能小车（二）—— Arduino 篇
---

Arduino，是一个开源的单板机控制器，采用了基于开放源代码的软硬件平台，构建于开放源代码 simple I/O接口版，并且具有使用类似Java，C语言的Processing Wiring开发环境。([维基百科](http://zh.wikipedia.org/wiki/Arduino))

选择Arduino作为智能小车的控制板，因为它的小巧但功能强大，能够方便的和传感器还有各种电子元件连接，而且还能和很多软件如Flash、Processing进行连接互动，但是最重要的是它的开源特性，它的电路设计图和IDE都是开源的，每个人都可以免费的修改和使用，这也是现在Arduino能够逐渐流行起来的原因，有什么比能够和别人自由地分享自己的制作更加有吸引力呢！

**Arduino Uno SMD** 意大利原装板

![](https://cdn.jczkids.com/Arduino%20Uno%20SMD%20%E6%84%8F%E5%A4%A7%E5%88%A9%E5%8E%9F%E8%A3%85%E6%9D%BF.jpg)

### 技术规格

*   工作电压：5V

*   微处理器：ATmega328

*   时钟频率：16 MHz

*   输入电压（推荐）：7-12V

*   输入电压（限制）：6-20V

*   数字I/O端口： 14（6个PWM输出口）

*   模拟输入端口： 6

*   直流电流 I/O端口： 40mA

*   直流电流 3.3V端口： 50mA

*   Flash 内存：32 KB (ATmega328) (0.5 KB用于引导程序Bootloader）

*   SRAM：2 KB (ATmega328)

*   EEPROM：1 KB (ATmega328)

*   尺寸：75x55x15mm

### 管脚功能

#### 电源引脚

*   VIN 当外部直流电源接入电源插座时，可以通过VIN向外部供电；也可以通过此引脚向UNO直接供电；VIN有电时将忽略从USB或者其他引脚接入的电源。

*   5V 通过稳压器或USB的5V电压，为UNO上的5V芯片供电。

*   3.3V 通过稳压器产生的3.3V电压，最大驱动电流50mA。

*   GND 地脚。

#### 输入输出引脚

1.  14路数字输入输出口：工作电压为5V，每一路能输出和接入最大电流为40mA。每一路配置了20-50K欧姆内部上拉电阻（默认不连接)。除此之外，有些引脚有特定的功能

    *   串口信号RX（0号）、TX（1号）: 与内部 ATmega8U2 USB-to-TTL 芯片相连，提供TTL电压水平的串口接收信号。

    *   外部中断（2号和3号）：触发中断引脚，可设成上升沿、下降沿或同时触发。

    *   脉冲宽度调制PWM（3、5、6、9、10 、11）：提供6路8位PWM输出。

    *   SPI（10(SS)，11(MOSI)，12(MISO)，13(SCK)）：SPI通信接口。

    *   LED（13号）：Arduino专门用于测试LED的保留接口，输出为高时点亮LED，反之输出为低时LED熄灭。

2.  6路模拟输入A0到A5：每一路具有10位的分辨率（即输入有1024个不同值），默认输入信号范围为0到5V，可以通过AREF调整输入上限。除此之外，有些引脚有特定功能

    *   TWI接口（SDA A4和SCL A5）：支持通信接口（兼容I2C总线）。

3.  AREF：模拟输入信号的参考电压。

4.  Reset：信号为低时复位单片机芯片。


### 测试程序

Arduino买回来的第一件事就是测试板子是否能正常工作，在这之前先要在电脑上安装好Arduino的IDE。

![](https://cdn.jczkids.com/Arduino%E7%9A%84IDE.jpg)

#### 安装方法

*   Ubuntu用户：在软件中心中搜索Arduino集成开发环境并安装，然后用USB线连接Arduino Uno和计算机，打开Arduino IDE，在主界面的工具栏选择Tools，Board这一项选择Arduino Uno，Serial Port 这一项选择板子使用的串口。

*   Windows 7用户：在官网下载最新版的软件包，解压缩后直接运行arduino.exe，用USB线连接Arduino Uno和计算机，会提示驱动安装失败，需要手动定位安装驱动，安装完成之后，在软件主界面的工具栏选择Tools，Board这一项选择Arduino Uno，Serial Port 这一项选择板子使用的串口。

安装完成之后依次点击Arduino IDE主界面的**File &nbsp;&gt; Examples &gt; 1.Basics &gt; Blink**就能打开示例的测试程序。

```c
/*
  Blink
  将一个LED打开一秒，然后关闭一秒。
*/

void setup() {                
  // 以数字输出方式启用Pin13
  // 大多数Arduino板的Pin13口连接着一个LED
  pinMode(13, OUTPUT);     
}

void loop() {
  digitalWrite(13, HIGH);   // 输出高电平，LED被点亮
  delay(1000);              // 延时1S
  digitalWrite(13, LOW);    // 输出低电平，LED被关闭
  delay(1000);              // 延时1S
}
```

每一个Arduino程序都必须拥有两个过程：**void setup{}()**和**void loop{}()**。当接通电源时，在void setup{}()里面的代码会执行一次，然后void loop{}()里面的代码会不断循环执行。

然后点击工具栏下面的Verify按钮，检查代码是否有错误，如果没有错误，点击Upload按钮，程序就会被写入板子里，写入的同时板子TX
RX指示灯会交替闪烁，当写入完成之后，你就会发现Pin13旁边的LED会以一秒为间隔闪烁。

Arduino就介绍到这里了，玩的愉快！

参考资料：

［1］Arduino Uno 介绍， [维基百科](http://zh.wikipedia.org/wiki/Arduino)，[ 官方文档](http://arduino.cc/en/Main/ArduinoBoardUno)， [
开源硬件知识库](http://kb.open.eefocus.com/index.php?title=Arduino_Uno "Arduino_Uno")

［2］Arduino IDE [下载](http://arduino.cc/en/Main/Software)及[安装](http://arduino.cc/en/Guide/HomePage)

［3］[Ubuntu安装Arduino及使用Emacs开发](http://arduino.cc/en/Guide/HomePage)

［4］[ARDUINO官网文档翻译](http://assiss.github.com/arduino-zhcn/)
