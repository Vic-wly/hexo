title: RPC 与 HTTP
author: Laiyong Wang
date: 2022-08-07 10:27:12
tags:
---
参考链接：https://blog.csdn.net/Solo95/article/details/122640662
RPC（即Remote Procedure Call，远程过程调用）和HTTP（HyperText Transfer Protocol，超文本传输协议），两者前者是一种方法，后者则是一种协议。两者都常用于实现服务，在这个层面最本质的区别是RPC服务主要工作在TCP协议之上（也可以在HTTP协议），而HTTP服务工作在HTTP协议之上。由于HTTP协议基于TCP协议，所以RPC服务天然比HTTP更轻量，效率更胜一筹。
两者都是基于网络实现的，从这一点上，都是基于Client/Server架构

RPC：
RPC服务基本架构包含了四个核心的组件，分别是Client,Server,Clent Stub以及Server Stub

![upload successful](/images/pasted-9.png)
RPC
效率优势明显，在实际开发中，客户端和服务端在技术方案中约定客户端的调用参数和服务端的返回参数之后就可以各自开发，任何客户端只要按照接口定义的规范发送入参都可以调用该RPC服务，服务端也能按接口定义的规范出参返回计算结果。这样既实现了客户端和服务端之间的解耦，也使得RPC接口可以在多个项目中重复利用
HTTP
基于http协议的post接口和get接口
简单、直接、开发方便，利用现成的HTTP协议进行传输

优劣点：
RPC： 1、报文小，传输效率快；2、统一调用，所以解耦；
HTTP： 灵活，跨语言、跨平台

如果对效率要求更高，并且开发过程使用统一的技术栈，那么RPC还是不错的
如果需要更加灵活，跨语言、跨平台，显然HTTP更合适