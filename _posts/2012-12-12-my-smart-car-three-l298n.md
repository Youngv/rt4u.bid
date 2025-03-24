---
layout: post
title: 我的智能小车（三）—— L298N 电机驱动篇
---

## 概述

由于我买的小车底板自带的是两个直流电机，而直流电机的驱动及控制需要电机驱动芯片进行驱动，常用的电机驱动芯片有 L297/298，MC33886，ML4428 等，而我采用的是 L298N 电机驱动芯片，下面就对 L298N 电机驱动芯片进行详细的讲解。

## L298N 芯片介绍

L298N 是 SGS 公司的产品，内部包含 4 通道逻辑驱动电路，是一种二相和四相电机的专用驱动器，即内含二个 H 桥的高电压大电流双全桥式驱动器，接收标准 TTL 逻辑电平信号，可驱动 46V、2A 以下的电机。

其实物及引脚图如下所示：

![L298N 芯片实物图](https://f002.backblazeb2.com/file/as-cdn/blog/L298N.jpg)

![L298N 引脚图](https://f002.backblazeb2.com/file/as-cdn/blog/L298N%E5%BC%95%E8%84%9A%E5%9B%BE.jpg)

### L298N 模块

我采用的是 L298N 模块，里面已经集成了 L298N 的驱动芯片，由于直流电机需要很大的电流才能驱动，所以 L298N 芯片工作时会产生很大的热量，一般 L298N 模块都会有一个散热片和 L298N 芯片连接在一起。下图是我使用的 L298N 驱动模块：

![L298N 驱动模块正面](https://f002.backblazeb2.com/file/as-cdn/blog/L298N%E5%8F%8C%E7%94%B5%E6%9C%BA%E9%A9%B1%E5%8A%A8%E6%A8%A1%E5%9D%97.jpg)

![L298N 驱动模块侧面](https://f002.backblazeb2.com/file/as-cdn/blog/L298N%E6%A8%A1%E5%9D%97.jpg)

### Arduino 扩展板

另外，Arduino 也有 L298N 电机驱动扩展板，可以和 Arduino 像积木一样拼起来，可以省下不少的连线，放图一张：

![L298N 电机驱动扩展板](https://f002.backblazeb2.com/file/as-cdn/blog/L298N%E7%94%B5%E6%9C%BA%E9%A9%B1%E5%8A%A8%E6%89%A9%E5%B1%95%E6%9D%BF.jpg)

## L298N 电机驱动模块控制方法

L298N 的 OUT1 和 OUT2 连接一个直流电机的两极，OUT3 和 OUT4 连接另一个直流电机的两极，这样 L298N 就和两个直流电机连接在一起，然后通过 Arduino 的 I/O 口给 IN1、IN2、IN3、IN4 分别输入高低电平就能控制电机的转动方向，下图是 L298N 电机驱动模块控制电机转动的编码图：

![L298N 驱动编码图](https://f002.backblazeb2.com/file/as-cdn/blog/L298N%E9%A9%B1%E5%8A%A8%E7%BC%96%E7%A0%81%E5%9B%BE.jpg)

### 转速控制

如果希望能够控制电机的转速，那么需要通过脉冲宽度调制（PWM）来控制电机的电压，从而实现电动机转速的控制，把 Arduino 的两个 PWM 输出口分别连接到 L298N 模块的 ENA 和 ENB，ENA 和 ENB 默认是和 5V 的高电平相连，也就是默认最大的转速。

### 供电注意事项

另外需要注意的是在供电方面由于直流电机需要很大的电流支持，所以需要一个大电流输出的电源，而不能通过 Arduino 来供电，我曾经使用四节五号电池来提供电流，但是使用不到一个小时，电池的电力就不足以提供足够的电流，所以我购买了一个 8V 3500MAh 的锂电池组来作为电源，然后通过 L298N 模块的 5V 输出口给 Arduino 供电。

## Arduino 编程实现

下面通过一个编程例子来介绍如何通过 Arduino 来控制小车的前进，加减速。

```c
/*
 * 智能小车电机控制示例
 * 这个范例让小车每 15 秒换一次方向，每次换方向就加速。
 */

// 电机 A
int dir1PinA = 13; // Arduino 的 13 和 12 号管脚分别连接 IN1 和 IN2
int dir2PinA = 12;
int speedPinA = 10; // Arduino 的 10 号 PWM 输出管脚连接 ENA

// 电机 B
int dir1PinB = 11; // Arduino 的 11 和 8 号管脚分别连接 IN3 和 IN4
int dir2PinB = 8;
int speedPinB = 9; // Arduino 的 9 号 PWM 输出管脚连接 ENB

int speed; // 定义速度变量，PWM 输出范围为 0～255
int dir; // 定义方向变量，若 1 为向前，则 0 为后退

void setup() {
  // 定义 8、9、10、11、12、13 管脚为数字输出方式
  pinMode(dir1PinA, OUTPUT);
  pinMode(dir2PinA, OUTPUT);
  pinMode(speedPinA, OUTPUT);
  pinMode(dir1PinB, OUTPUT);
  pinMode(dir2PinB, OUTPUT);
  pinMode(speedPinB, OUTPUT);

  speed = 0; // 初始化速度为 0
  dir = 1; // 初始化方向
}

void loop() {
  analogWrite(speedPinA, speed); // 输出 PWM 脉冲到 ENA
  analogWrite(speedPinB, speed); // 输出 PWM 脉冲到 ENB
  
  if (1 == dir) {       // 若方向为 1，8、12 输出高电平，11、13 输出低电平
    digitalWrite(dir1PinA, LOW);
    digitalWrite(dir2PinA, HIGH);
    digitalWrite(dir1PinB, LOW);
    digitalWrite(dir2PinB, HIGH);
  } else {              // 方向为 0，8、12 输出低电平，11、13 输出高电平
    digitalWrite(dir1PinA, HIGH);
    digitalWrite(dir2PinA, LOW);
    digitalWrite(dir1PinB, HIGH);
    digitalWrite(dir2PinB, LOW);
  }
  
  speed += 20;          // 加速
  if (speed > 255) {    // 速度达到最大值 255 则归零
    speed = 0;          
  }
  
  if (1 == dir) {       // 方向反向
    dir = 0;
  } else {
    dir = 1;
  }
  
  delay(15000);         // 延时 15 秒
}
```

通过这个范例，我们可以看出用 Arduino 来控制小车的方向和速度是非常容易的，同样控制小车转弯只需要其中一个电机转动或者两个电机转动方向不同就行了，至于更加复杂的动作就是把最基本的动作组合起来，如果为了方便以后使用程序，可以把电机的控制写成一个函数，这不，早已经有人做好了一个电机控制的库 Motor Library for Arduino 供大家使用，Arduino 开源的好处就体现出来了。

## 参考资料

1. [基于 Arduino 和 labview 的直流电机控制器的实验](http://www.eefocus.com/zhang700309/blog/2012-02/233951_d6fd7.html)，宜昌城老张的博客
2. [Arduino 与 L298N 直流电机驱动板](http://blog.163.com/hmfile@126/blog/static/16868626920115741935430/)，黑马工作
3. [Motor Library for Arduino](http://www.arduino.cc/playground/Code/Motor#CurrentVersion)，Arduino Playground
