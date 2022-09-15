title: 路由解析
author: Laiyong Wang
date: 2022-08-06 15:10:57
tags:
---
url 到程序实际运行需要两步
1、路由解析
laravel api.php里注册，根据请求 method 和路径与控制器的方法做映射对应
正常系统，根据 url 直接确定文件及方法，一步到位
2、请求分发

PS：第一步的路由解析，正常情况下，得符合 restful 接口规范