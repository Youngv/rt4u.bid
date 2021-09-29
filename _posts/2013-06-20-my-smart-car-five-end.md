---
title: 我的智能小车（五）——终结篇
---
时隔整整八个月，终于想起要把最终的成果放上来，其实这个东西已经完成很久了，久到我自己都快要忘记我曾经花费了很多时间在这个项目上，最后我只想把最终的作品放上来，其实我还有很多预想的功能还没有完成，但是由于种种原因，我没有继续做下去，这也算是一个半成品吧。

### 最终程序：

```c
//测速模块
int SpeedNumLeft = 12; //连接到pin 12
int SpeedNmuRight = 13; //连接到pin 13
//左轮驱动
int MotorLeftPinA = 2;
int MotorLeftPinB = 4;
int MotorLeftEn = 3;
//右轮驱动
int MotorRightPinA = 7;
int MotorRightPinB = 8;
int MotorRightEn = 6;

int Motor;
int Speed = 100;
int dir;

int i;
int j;
int x;
int y;

int val;

int n = 0; //码盘读数（即状态跳变次数）
int m = 0; //码盘读数（即状态跳变次数）
int flagL = 0; //因为初始读数可能为0或1，该标志是用来读取初始状态的
int flagR = 0; //因为初始读数可能为0或1，该标志是用来读取初始状态的

void setup()
{
    Serial.begin(9600);

    pinMode(MotorLeftPinA, OUTPUT);
    pinMode(MotorLeftPinB, OUTPUT);
    pinMode(MotorLeftEn, OUTPUT);

    pinMode(MotorRightPinA, OUTPUT);
    pinMode(MotorRightPinB, OUTPUT);
    pinMode(MotorRightEn, OUTPUT);

    pinMode(SpeedNumLeft, INPUT);
    pinMode(SpeedNmuRight, INPUT);  
}

//电机驱动函数
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

//码盘函数
void SpeedNum(char motor)
{
    if(motor == 'L')
    {
        i = digitalRead(SpeedNumLeft); //读取码盘状态（0或1）
        if(!flagL){ //获取初始状态读数
            j = i;
            flagL = 1;
        }

        if(i != j){ //每次状态发生跳变的时候，读数增加1
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
        x = digitalRead(SpeedNmuRight); //读取码盘状态（0或1）
        if(!flagR){ //获取初始状态读数
            j = y;
            flagR = 1;
        }

        if(x != y){ //每次状态发生跳变的时候，读数增加1
            m += 1;
            y = x;
            Serial.print("Right:");
            Serial.println(m);
            Serial.print("Speed:");
            Serial.println(Speed);
        }
    }
}

//前进
void Forward()
{
    MOTORDRIVE('R',Speed,1);
    MOTORDRIVE('L',Speed,1);
}
//后退
void Backward()
{
    MOTORDRIVE('R',100,0);
    MOTORDRIVE('L',100,0);
}
//停止
void Stop()
{
    MOTORDRIVE('R',0,1);
    MOTORDRIVE('L',0,1);    
}
//右转
void RTurn()
{
    Stop();
    MOTORDRIVE('L',100,1);
}
//左转
void LTurn()
{
    Stop();
    MOTORDRIVE('R',100,1);
}
//加速
void SpeedUp()
{
    Speed += 10;
}
//减速
void SpeedDown()
{
    Speed -= 10;
}

void loop()
{
    SpeedNum('R');
    SpeedNum('L');
    if(Serial.available())
    {
        val = Serial.read();
        if(val == 'f'){
            Forward();
        }
        if(val == 's'){
            Stop();
        }
        if(val == 'b')
        {
            Backward();
        }
        if(val == 'l')
        {
            LTurn();
        }
        if(val == 'r')
        {
            RTurn();
        }
        if(val == 'o')
        {
            SpeedUp();
        }
        if(val == 'p')
        {
            SpeedDown();
        }
    }
}
```

### 最终APP：

![](https://f002.backblazeb2.com/file/as-cdn/blog/APP%E7%95%8C%E9%9D%A2.jpg)

### 演示视频

<video controls width="640" height="360" preload="none">
  <source src="https://f002.backblazeb2.com/file/as-cdn/blog/Arduino%2BAndroid%20%E8%93%9D%E7%89%99%E9%81%A5%E6%8E%A7%E5%B0%8F%E8%BD%A6.mp4" type="video/mp4">
  Your browser does not support the <code>video</code> element.
</video>

### 相关下载

+   Arduino程序下载地址：[七牛云](https://f002.backblazeb2.com/file/as-cdn/blog/ArduinoCar.ino)

+   安卓APP下载程序：[七牛云](https://f002.backblazeb2.com/file/as-cdn/blog/BlueCar.apk)

+   Appinventor程序下载地址：[七牛云](https://f002.backblazeb2.com/file/as-cdn/blog/BlueCar.zip)

+   研究性报告：[七牛云](https://f002.backblazeb2.com/file/as-cdn/blog/%E7%A0%94%E7%A9%B6%E6%80%A7%E6%8A%A5%E5%91%8A.pdf)
