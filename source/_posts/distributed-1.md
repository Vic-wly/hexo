title: 分布式、微服务简述
author: Laiyong Wang
date: 2022-09-05 17:28:42
tags:
---
#### PS：以现在服务器的强度，需要用到微服务的公司能有多少,持怀疑态度，感觉是为了不同业务组甩锅时好甩
#### 分布式
分布式系统由一组为了完成共同任务而协调工作的计算机节点组成，它们之间通过网络进行通信。分布式系统能满足互联网对大数据存储、高并发和快响应的要求，采用了分而治之的思想
##### 分布式切分方法
- 水平切分
多个节点部署同样的系统，请求由网管路由来决定最终进入那个节点
- 垂直切分
将一个系统分成多个模块，分别部署在不同的节点上，请求分别请求不同的节点，节点之间使用网络进行通信
- 混合切分
就是水平切分和垂直切法的混合模式，将垂直拆分的模块进行水平拆分，那就说混合拆分

#### 微服务
原则上来说，微服务是分布式系统设计和架构的理念之一，主要方法是垂直切分，实际项目中都是以微服务架构进行开发

微服务架构只是将一个单体应用程序拆分为多个相对独立的服务，每一个服务拥有独立的进程和数据，每一个服务都是以轻量级的通信机制（正常都是 rpc ）进行交互

#### CAP理论
一致性（consistency）：保持所有节点在同一个时刻具有相同的、逻辑一致的数据。
可用性（availability）：保证每个请求不管成功还是失败都有响应。
分区容忍性（partition tolerance）：系统中任何的信息丢失或者失败都不会影响系统的继续运作

CA：满足一致性和可用性的系统。在可扩展性上难有建树。
CP：满足一致性和分区容忍性的系统。通常性能不是特别高。
AP：满足可用性和分区容忍性的系统。通常对一致性要求低一些，但性能会比较高

任何的分布式系统都只能较好地完成其中的两个指标，无法完成 3 个指标。
在当今互联网中，保持可用性往往是第一位的，其次是性能。因为从客户的感知来说，可用和快速响应能够提供更好的体验。一致性可以通过其他手段来保证，保证最终一致性就行