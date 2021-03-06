# 激光雷达避障小车 #
声明：本作品为 来一颗糖 原创。

## 背景描述 ##
由于在学校里很少有机会让我将所学的东西付诸于实践.有时候学完一个东西没法真正了解自己是否掌握,同时为了进一步提高自己的能力,不再漫无目的的学习.所以决定做这样一辆小车.其中PCB原理图和初版PCB_layout是我在寒假在家中完成的,程序是基于rt-thread[BSP]stm32f429-Apollo写的.历时3个多月.这辆小车很多地方可以说是边学边做的,尤其是有关于rt-thread方面的知识.在学习 rt-thread 期间受到了很多RTT社区里的人的帮助.要特别感谢下RTT开源社区.

## RT-Thread起到的作用 ##
作为主控芯片的实时系统,提供多线程编程.小车的每个重要的需要实时的功能都单独作为一个线程.如小车的mpu9250姿态解算出姿态角（Roll、Pitch、Yaw ）的过程就单独使用了一个线程(mpu9250),小车的PID控制速度的代码也单独使用了一个线程(speed).每个功能线程(mpu9250,speed...等)都会处理完各自的数据得出结果,并且这些结果在必要的时候提供给主线程(master)使用.也正是因为rt-thread的优先级全抢占式调度使得重要的线程能及时处理完.另外rt-thread提供的finsh/msh在调试期间起到了很大的作用,同时也可以通过远程蓝牙串口控制小车的行为.
## 实物图 ##
![](https://i.imgur.com/QMd45fV.jpg)
![](https://i.imgur.com/5vmUJ0z.jpg)
![](https://i.imgur.com/Y4PIY6w.jpg)

- 主控芯片:STM32F429IGT6
- 其它主要配件:激光雷达,蓝牙串口,无线射频模块,MPU9250九轴姿态模块,电机驱动芯片l298n.两个自带AB相编码器的电机.履带一对.
- 编译环境:MDK525
- RT-Thread版本:3.0.2

## 硬件设计 ##
![](https://i.imgur.com/5kIVzkE.png)


1. 激光雷达通过串口通讯,以及pwm控制转速.
2. 射频模块通过uart6通信.使用rt-thread的事件通知消息的到来.
3. 蓝牙串口,作为msh终端的输出.方便调试.
4. mpu9250模块通过i2c通信.
5. ![](https://i.imgur.com/KT9gh5y.png)
6. ![](https://i.imgur.com/QTtxEFG.png)


## 软件设计 ##
![](https://i.imgur.com/v9nbnVR.png)

1. master线程负责创建其它子线程,以及处理各种子线程处理后的信息.
2. eaix4线程控制激光雷达上传到消息队列里的消息,将激光雷达的版本信息,状态,以及扫描数据解析出来.
3. mpu9250线程通过dmp姿态解算出 姿态角（Roll、Pitch、Yaw ）.
4. speed线程通过采集stm429自带的编码器以及pwm输出,PID控制电机转速.

## 几个主要的命令 ##

| 命令| 功能                   |
| ---------- | ---------------------------- |
|eaix4cmd -gvf   | 输出激光雷达版本信息.|
|eaix4cmd -sc    | 小车开始扫描,启动避障功能.|
|eaix4cmd -s     | 激光雷达停止扫描,但是不会停车.|
|carMove  -f     | 小车向前.|
|carMove  -b     | 小车后退.|
|carMove  -l     | 小车左转.|
|carMove  -r    |  小车右转.|

## 操作演示GIF ##
![](https://i.imgur.com/eqzMpVY.gif)
## 小车效果 ##
![](https://i.imgur.com/zro4fZz.gif)
## 源码以及PCB ##
因为个人所学有限,所以仅仅是实现了小车自主避障,希望将来我有这个能力去继续完善,实现更多功能.(pcb文件夹里有原理图和pcb).
> [https://github.com/balanceTWK/LidarCar.git](https://github.com/balanceTWK/LidarCar.git)

将下载下来的代码放在rt-thread-3.0.2\bsp\目录下,直接打开project.uvprojx文件即可.或者在我的百度云里下载完整的代码.
百度云地址:[https://pan.baidu.com/s/1he8LQZstRXENBMgs_tQ5Mw](https://pan.baidu.com/s/1he8LQZstRXENBMgs_tQ5Mw)

