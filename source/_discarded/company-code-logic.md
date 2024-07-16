title: 工作代码梳理
author: Laiyong Wang
date: 2022-10-18 14:22:17
tags:
---
### 控制器文件加载逻辑
```
//顺序执行
yii\web\Application::handleRequest
yii\base\Module::runAction
yii\base\Controller::runAction
common\base\Controller::createAction //这个决定加载那个控制器文件，代码逻辑如下图
```
#### 登录验证逻辑
举个例子
```

```
文件地址 : framework/src/common/base/Controller.php

![upload successful](/images/pasted-22.png)

#### 中心管理的逻辑
```
//创建，会先后创建中中心、机构、用户、机构用户这四个表的数据,还会检查是否有部中心的书机构，无则创建
//可以创建下级机构,类似与无限极分类，机构类型有 部中心(1,不用了)，省中心(2)，地市中心(3)、市级机构(5)、省级机构(4)
//鉴定考务配置平台的每个机构都会有对应的信息，可编辑，也有一一对应的用户，可直接登录ATA鉴定考务平台
//机构肯定需要有业务，那就得有鉴定范围（kw_org_range），得从 kw_dict_worktype 选
```

