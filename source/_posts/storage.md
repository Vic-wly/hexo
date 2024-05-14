title: 存储器的种类和速度
author: Laiyong Wang
date: 2024-05-08 15:02:31
tags:
---
#### 种类
  大致可分为机械硬盘、固态硬盘、内存三种存储器，cpu内也有存储数据的组件：寄存器、CPU L1/L2/L3 Cache，
#### 硬件介质（用的啥保存数据）
  寄存器和CPU Cache 用的是 SRAM（Static Random-Access Memory，静态随机存储器） 的芯片
  内存用的是 DRAM （Dynamic Random Access Memory，动态随机存取存储器） 的芯片
  固体硬盘SSD（Solid-state disk）
  机械硬盘HDD（Hard Disk Drive）
  
#### 断电区别（这点经常在redis的面试里问，这个其实算是原理）
  机械硬盘、固态硬盘可持久化保存数据，内存不可持久化保存，断电则消失，cpu里的存储也是断电即消失
  
#### 数据量
  机械硬盘、固态硬盘保存的数据量非常大（正常单位是TB），内存较大（正常单位是为GB），寄存器及CPU Cache 很小，通常是几十kb只几mb之间
  原因：HDD、SSD成本很便宜，DRAM成本较贵，SRAM成本很贵
#### 速度
  我们常说的读取/写入速度，其实也就是CPU执行程序时取数据的时间，L1 Cache的访问速度几乎和寄存器一样快，但是没它快。
  注意：CPU Cache 的数据是从内存加载过来的，写回数据的时候也只写回到内存。硬盘的数据无论读取还是写入都会先一步到内存，参考下方图片可得
  SSD 比机械硬盘快 70 倍左右；
  内存比机械硬盘快 100000 倍左右；
  CPU L1 Cache 比机械硬盘快 10000000 倍左右
  
![upload successful](/images/pasted-36.png)
  