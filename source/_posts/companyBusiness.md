title: 业务介绍-简版
author: Laiyong Wang
date: 2022-08-30 20:38:31
tags:
---
#### 第一家公司
负责 oms erp 部分模块的优化和迭代，日常是与第三方电商平台（淘宝、京东、寺库）做对接，同时也与品牌方的仓库系统对接
仅退款自动化（根据发货状态判断，然后订单表新增字段实际支付金额，退款时自动编辑订单），退货退款部分流程自动化，例如创建、审核、退货签收等节点，根据已有信息进行判断是否自动通过，然后根据平台类型决定同步至对应平台
期间也合作开发过几款小程序，主要是为他们提供后端接口

#### 第二家公司
跟视频和直播有关
主要工作内容是调用公司或第三方厂商的转码和检测服务
视频的定制化转码和检测
直播的定制化转码和加些logo广告信息
同时转码的数据进行，数据监控及数据统计模块和视频的流转做成拓扑图供运营人员查看

#### 第三家公司
公司自有平台，主要售卖有关家居建材的装修产品
我在里面负责售后、物流单的指派和物流单的状态流转模块

然后我换了个业务组去做库存中心的项目
负责渠道层和缺货商品模块
渠道层主要功能是为商品模块提供库存和库存是否够下单环节提供是否可下单
库存由云仓层同步

缺货商品模块，是因为渠道层允许设置虚拟库存进行超卖，所以超卖的商品及订单地址等信息，进行整合后提供给采购查看，看离哪些仓库近然后去进货
