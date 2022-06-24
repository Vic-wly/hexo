title: 浅谈  依赖注入 控制反转 ioc容器
author: Laiyong Wang
date: 2022-06-10 10:19:30
tags:
---
#### ioc 控制反转
&ensp;&ensp;&ensp;&ensp;参数由调用者控制（生成），而非被实例的对象（被调用的代码）来进行创建


#### DI  依赖注入
&ensp;&ensp;&ensp;&ensp;只要不是由内部生产（比如初始化、构造函数 __construct 中通过工厂方法、自行手动 new 的），而是由外部以参数或其他形式注入的，都属于 依赖注入（DI）
	

#### container 容器
&ensp;&ensp;&ensp;&ensp;如汉字意思，容，需要容纳很多东西，还支持取出

#### ioc 容器
&ensp;&ensp;&ensp;&ensp;在架构上，主要使用的功能有两点
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;绑定：单例模式，写入然后返回
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;解析：看是否一绑定，有了之后直接返回，没有直接走绑定流程
#### ioc 容器实现自动依赖注入
&ensp;&ensp;使用时，参数填两个，一个类型 一个值，通过 php 的反射机制，返回实例化之后的对象，具体流程如下
&ensp;&ensp;&ensp;&ensp;IoC 控制反转:
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;控制：容器控制了对象的创建
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;反转：创建对象的权利已经转移到了容器中来了
&ensp;&ensp;&ensp;&ensp;DI 依赖注入:
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;依赖：容器中的一个属性保存了需要那些依赖
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;注入：把 属性中保存的依赖作为参数传入(注入)，返回实例