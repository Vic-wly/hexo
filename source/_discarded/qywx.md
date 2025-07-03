title: 企业微信客服结合微信对话开放平台实现自动回复消息（待更新）
author: Laiyong Wang
date: 2024-10-16 11:06:36
tags:
---
#### 分配客服会话
获取会话状态然后决定是智能机器人&待接入池排队中&分配客服
https://developer.work.weixin.qq.com/document/path/94669

#### 接收消息和事件
接收消息使用的是统一的，但是客服有具体的区别
消息的类型，此时固定为：event
事件的类型，此时固定为：kf_msg_or_event
https://developer.work.weixin.qq.com/document/path/90238

#### 发送消息
https://developer.work.weixin.qq.com/document/path/94677