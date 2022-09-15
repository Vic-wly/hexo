title: laravel 知识点
author: Laiyong Wang
date: 2022-08-20 23:56:46
tags:
---
#### laravel 9.X 中文文档
https://learnku.com/docs/laravel/9.x/lifecycle/12205
#### laravel 的生命周期
1、laravel 的生命周期都是从 public/index.php 开始，该文件将加载 Composer 生成的自动加载器定义，然后从 bootstrap/app.php 中检索 Laravel 应用程序的实例，Laravel 本身采取的第一个操作是创建应用/服务容器 的实例
2、请求发送到 http 内核或者 console 内核（分别处理 web 请求和 artisan 命令）。内核请求过程中最重要的内容就是为应用/服务容器载入服务提供者。应用/服务容器中所有的服务提供者都被配置在 config/app 配置文件的 providers 数组中
3、分发请求，应用被启动且所有服务提供者被注册，request 将会给路由器分发，路由器将会分发请求到路由或者控制器，同时运行所有路由指定的中间件
#### 服务容器
这玩意吧，怎么研究怎么像 IOC 容器，查了下，是 IOC 容器改名成服务容器了，真是日了狗了
参考我的另一片文章：http://laiyong.wang/2022/06/10/laravelCoreIdea
#### 服务提供者
名字取的好听，看了半天，其实就相当于 app.php 文件里的 providers 里注册的东西自动加载 and 提前加载
大概意思就是将 providers 里的东西注册绑定至 IOC 容器中，使用时直接在容器中解析就好
#### Facades
场面话 Facades 为应用程序的 服务容器 中可用的类提供了「静态代理」
白话文就是：可以直接 className::functionName() 使用部分服务容器里的方法了，具体如何使用，可以去 vendor/laravel/framework/src/Illuminate/Support/Facades/*** 里研究下，哪些可用且其对应的类是那个，最终使用的方法是那个

#### laravel 路由隐式绑定的原理
官方文档解释：Laravel 会自动解析定义在路由或控制器行为中与类型提示的变量名匹配的路由段名称的 Eloquent 模型
白话文大概意思就是 url 中的单词与 Eloquent 模型匹配，相同的话自动绑定
eg：单词 user ，Eloquent 模型 user
```
Route::get('users/{user}', UsersController@show);
```
- 显式绑定
既然有隐式绑定，反义词必须要安排
例如不想用 user ，非要用 wanglaiyong ，那就重写模型文件 app/Providers/RouteServiceProvider.php 中的 getRouteKeyName 方法
```
public function boot()
{
    parent::boot();
    Route::model('wanglaiyong', \App\User::class);
}
```
#### 事件系统
事件与监听通常是搭配使用，可以将其理解为观察者模式
主要目的，我认为是为了解藕，关键是其即可以同步执行也可以异步执行，异步的时候还可以塞队列，队列名还可以自定义，而且还可以使用队列的各种补偿机制。最关键的是不用跟别人的代码写在一起，简直完美
#### Storage
常用的就是上传下载，生成文件用 composer 第三方的代码
日常工作中，基本都是 excel 文件
####  Composer aotuload 的原理
composer 加载核心思想是通过 composer 的配置文件在引用入口文件 (autoload.php) 时，将类和路径的对应关系加载到内存中，最后将具体加载的实现注册到 spl_autoload_register 函数中。最后将需要的文件包含进来
####  ORM 代表什么
对象关系映射，把数据库相关字段映射到对应的模型对象里面，相当于有多个一个抽象层。后面直接操作对象
laravel 使用的是 Eloquent
#### DB Facade 的用途
用于运行 SQL 查询，例如创建，查询，更新，插入和删除。主要是用到了门面的静态代理，通过门面把相关类库代理到容器里面的操作类库
#### 反射
根据类名返回该类的任何信息，它主要用来动态地获取系统中类、实例对象、方法等语言构件的信息，通过反射API函数可以实现对这些语言构件信息的动态获取和动态操作等。同时反射添加了对类、接口、函数、方法和扩展进行反向工作的能力
#### Horizon

#### 再整些面试可能问的

![upload successful](/images/pasted-11.png)

![upload successful](/images/pasted-12.png)

![upload successful](/images/pasted-13.png)

![upload successful](/images/pasted-14.png)

![upload successful](/images/pasted-15.png)