---
layout: single
toc: true
title: 我的智能小车（五）——终结篇
---

## 项目总结

时隔整整八个月，终于想起要把最终的成果放上来，其实这个东西已经完成很久了，久到我自己都快要忘记我曾经花费了很多时间在这个项目上，最后我只想把最终的作品放上来，其实我还有很多预想的功能还没有完成，但是由于种种原因，我没有继续做下去，这也算是一个半成品吧。

## 最终程序

完整的 Arduino 代码如下：

```c
/*
 * 智能小车完整控制程序
 * 包含电机驱动、测速及蓝牙控制功能
 */

// 测速模块
int SpeedNumLeft = 12;  // 连接到 pin 12
int SpeedNmuRight = 13; // 连接到 pin 13

// 左轮驱动
int MotorLeftPinA = 2;
int MotorLeftPinB = 4;
int MotorLeftEn = 3;

// 右轮驱动
int MotorRightPinA = 7;
int MotorRightPinB = 8;
int MotorRightEn = 6;

int Motor;
int Speed = 100;  // 初始速度值
int dir;

int i;
int j;
int x;
int y;

int val;  // 存储蓝牙接收的指令

int n = 0;      // 左轮码盘读数（即状态跳变次数）
int m = 0;      // 右轮码盘读数（即状态跳变次数）
int flagL = 0;  // 左轮初始状态标志
int flagR = 0;  // 右轮初始状态标志

void setup()
{
    Serial.begin(9600);  // 初始化串口通信，用于蓝牙控制

    // 初始化左轮控制引脚
    pinMode(MotorLeftPinA, OUTPUT);
    pinMode(MotorLeftPinB, OUTPUT);
    pinMode(MotorLeftEn, OUTPUT);

    // 初始化右轮控制引脚
    pinMode(MotorRightPinA, OUTPUT);
    pinMode(MotorRightPinB, OUTPUT);
    pinMode(MotorRightEn, OUTPUT);

    // 初始化测速模块引脚
    pinMode(SpeedNumLeft, INPUT);
    pinMode(SpeedNmuRight, INPUT);  
}

/**
 * 电机驱动函数
 * @param motor 电机选择，'L'为左轮，'R'为右轮
 * @param speed 速度值 (0-255)
 * @param dir   方向，1 为正转，0 为反转
 */
void MOTORDRIVE(char motor, int speed, int dir)
{
    if(motor == 'L')    
    {
        analogWrite(MotorLeftEn, speed);
        if(dir == 1)
        {
            digitalWrite(MotorLeftPinA, HIGH);
            digitalWrite(MotorLeftPinB, LOW);
        }
        else
        {
            digitalWrite(MotorLeftPinA, LOW);
            digitalWrite(MotorLeftPinB, HIGH);
        }
    }

    if(motor == 'R')    
    {
        analogWrite(MotorRightEn, speed);
        if (dir == 1)
        {
            digitalWrite(MotorRightPinA, HIGH);
            digitalWrite(MotorRightPinB, LOW);
        }
        else
        {
            digitalWrite(MotorRightPinA, LOW);
            digitalWrite(MotorRightPinB, HIGH);
        }
    }
}

/**
 * 码盘读数函数
 * @param motor 电机选择，'L'为左轮，'R'为右轮
 */
void SpeedNum(char motor)
{
    if(motor == 'L')
    {
        i = digitalRead(SpeedNumLeft);  // 读取码盘状态（0 或 1）
        if(!flagL){  // 获取初始状态读数
            j = i;
            flagL = 1;
        }

        if(i != j){  // 每次状态发生跳变的时候，读数增加 1
            n += 1;
            j = i;
            Serial.print("Left:");
            Serial.println(n);
            Serial.print("Speed:");
            Serial.println(Speed);
        }
    }

    if(motor == 'R')
    {
        x = digitalRead(SpeedNmuRight);  // 读取码盘状态（0 或 1）
        if(!flagR){  // 获取初始状态读数
            y = x;
            flagR = 1;
        }

        if(x != y){  // 每次状态发生跳变的时候，读数增加 1
            m += 1;
            y = x;
            Serial.print("Right:");
            Serial.println(m);
            Serial.print("Speed:");
            Serial.println(Speed);
        }
    }
}

// 前进函数
void Forward()
{
    MOTORDRIVE('R', Speed, 1);
    MOTORDRIVE('L', Speed, 1);
}

// 后退函数
void Backward()
{
    MOTORDRIVE('R', 100, 0);
    MOTORDRIVE('L', 100, 0);
}

// 停止函数
void Stop()
{
    MOTORDRIVE('R', 0, 1);
    MOTORDRIVE('L', 0, 1);    
}

// 右转函数
void RTurn()
{
    Stop();
    MOTORDRIVE('L', 100, 1);
}

// 左转函数
void LTurn()
{
    Stop();
    MOTORDRIVE('R', 100, 1);
}

// 加速函数
void SpeedUp()
{
    Speed += 10;
}

// 减速函数
void SpeedDown()
{
    Speed -= 10;
}

void loop()
{
    // 持续读取码盘数据
    SpeedNum('R');
    SpeedNum('L');
    
    // 检查是否有蓝牙数据
    if(Serial.available())
    {
        val = Serial.read();
        if(val == 'f'){
            Forward();  // 前进
        }
        if(val == 's'){
            Stop();     // 停止
        }
        if(val == 'b')
        {
            Backward(); // 后退
        }
        if(val == 'l')
        {
            LTurn();    // 左转
        }
        if(val == 'r')
        {
            RTurn();    // 右转
        }
        if(val == 'o')
        {
            SpeedUp();  // 加速
        }
        if(val == 'p')
        {
            SpeedDown(); // 减速
        }
    }
}
```

## 控制应用界面

为了方便控制小车，我开发了一个 Android 手机应用，通过蓝牙与小车连接并进行远程控制。

![APP 控制界面](https://f002.backblazeb2.com/file/as-cdn/blog/APP%E7%95%8C%E9%9D%A2.jpg)

## 演示视频

下面是智能小车的操作演示视频：

<video controls width="640" height="360" preload="none">
  <source src="https://f002.backblazeb2.com/file/as-cdn/blog/Arduino%2BAndroid%20%E8%93%9D%E7%89%99%E9%81%A5%E6%8E%A7%E5%B0%8F%E8%BD%A6.mp4" type="video/mp4">
  您的浏览器不支持 <code>video</code> 元素。
</video>

## 相关资源下载

以下是项目相关文件的下载地址：

1. **Arduino 程序**：[下载地址](https://f002.backblazeb2.com/file/as-cdn/blog/ArduinoCar.ino)
2. **Android 应用 APK**：[下载地址](https://f002.backblazeb2.com/file/as-cdn/blog/BlueCar.apk)
3. **App Inventor 项目文件**：[下载地址](https://f002.backblazeb2.com/file/as-cdn/blog/BlueCar.zip)
4. **研究性报告**：[下载地址](https://f002.backblazeb2.com/file/as-cdn/blog/%E7%A0%94%E7%A9%B6%E6%80%A7%E6%8A%A5%E5%91%8A.pdf)

## 项目反思

虽然这个智能小车项目只是一个半成品，但通过它我学习了很多关于 Arduino 编程、电机驱动控制、Android 应用开发以及蓝牙通信的知识。未来如果有机会，我希望能够继续完善以下功能：

1. 添加更多传感器（如超声波）实现避障功能
2. 优化速度控制算法，实现更平稳的行驶
3. 增加自动巡线功能
4. 扩展手机应用的控制功能
