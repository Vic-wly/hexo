title: 浅谈  依赖注入 控制反转 ioc容器
author: Laiyong Wang
tags:
  - 依赖注入
  - 控制反转
categories: []
date: 2022-06-10 10:19:00
---
#### ioc 控制反转
&ensp;&ensp;&ensp;&ensp;参数由调用者控制（生成），而非被实例的对象（被调用的代码）来进行创建
&ensp;&ensp;&ensp;&ensp;大概意思就是，不管咋地，反正不能是被调用的对象（代码）来控制生成，必须有调用方或者中间层（容器）来控制生成


#### DI  依赖注入
&ensp;&ensp;&ensp;&ensp;一种对象依赖于另一个对象，只要不是由内部生产（比如初始化、构造函数 __construct 中通过工厂方法、自行手动 new 的），而是由外部以参数或其他形式注入的，都属于 依赖注入（DI）
- 三种注入类型
1、 构造函数注入
2、setter 注入
3、接口注入
	

#### container 容器
&ensp;&ensp;&ensp;&ensp;如汉字意思，容，需要容纳很多东西，还支持取出

#### ioc 容器
&ensp;&ensp;&ensp;&ensp;解释：被调用的别想着自己控制对象的生成，我们帮你完成
&ensp;&ensp;&ensp;&ensp;主要的功能有两点
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;绑定：单例模式，写入然后返回
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;解析：看是否已绑定，有了之后直接返回，没有直接走绑定流程
#### ioc 容器实现自动依赖注入
&ensp;&ensp;使用时，参数填两个，一个类型 一个值，通过 php 的反射机制，返回实例化之后的对象，具体流程如下
&ensp;&ensp;&ensp;&ensp;IoC 控制反转:
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;控制：容器控制了对象的创建
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;反转：创建对象的权利已经转移到了容器中来了
&ensp;&ensp;&ensp;&ensp;DI 依赖注入:
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;依赖：容器中的一个属性保存了需要那些依赖
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;注入：把 属性中保存的依赖作为参数传入(注入)，返回实例