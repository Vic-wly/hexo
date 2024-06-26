title: 重温基础：操作系统
author: Laiyong Wang
date: 2024-04-28 15:57:13
tags:
---
# 感谢图灵和冯诺依曼两位大佬赏饭吃
#### 本文只记录我个人知道且感觉需要知道的知识点，系统学习请网上查找资料

1. 计算机基本结构（也叫冯诺依曼模型）
	<b>运算器、控制器、存储器、输入设备、输出设备<b>
    存储器：我们常说的内存
    运算器、控制器：我们常说的CPU（中央处理器），有寄存器、控制单元和逻辑运算单元等组件
    输入设备、输出设备：鼠标、键盘、显示器...
    
2. 32位和64位的区别
	32 和 64 指的是CPU 的位宽，代表的是 CPU 一次可以计算（运算）的数据量，所以64位（0～2^64-1）的电脑可以兼容32（0～2^32-1）的软件，反之不行

3. 寄存器
	是一种内存，存在于cpu里面的内存
    常见的有通用寄存器（存放需要进行运算的数据），程序计数器（存放下一个指令的内存地址），指令寄存器（存放当前正在执行的指令）。
    
4. GHz（CPU的参数）,千兆赫兹的简写
	1GHz=1000000000Hz
	1GHz，代表着 1 秒会产生 1G 次数的脉冲信号，每一次脉冲信号高低电平的转换就是一个周期，称为时钟周期
    程序执行时间=指令数量*每个指令需要的是时钟周期*时钟周期
    结论：买电脑和服务器选那种赫兹数大的选，还有架构不同也会影响速度，涉及知识盲区了
    
5. 存储器
  看另一篇文章
  http://laiyong.wang/2024/05/08/storage