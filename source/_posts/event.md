title: laravel 事件之观察者模式
author: Laiyong Wang
date: 2022-01-26 11:10:59
tags:
---
#### 注意事项，必须注意
本文的代码里事件及监听器是同步执行，所以会捕捉到监听器的抛错
若事件塞进数据库或者队列里，异步执行，会捕捉不到监听器的抛错且事件执行成功

#### 系统的服务提供者 App\Providers\EventServiceProvider

```
use App\Events\Wang;
use App\Listeners\Yong;

/**
 * 系统中的事件和监听器的对应关系.
 *
 * @var array
 */
protected $listen = [
    Wang::class => [
        Yong::class,
        //·······可新增，一个事件可以有多个监听者
    ],
];
```

#### 批量生成事件和监听器
将监听器和事件添加到 EventServiceProvider 并使用 event:generate Artisan 命令。此命令将生成 EventServiceProvider 中列出的、尚不存在的任何事件或监听器
```
php artisan event:generate
```
#### 单个生成事件或者监听器
```
php artisan make:event Wang

php artisan make:listener Yong --event=Wang
```
#### 测试流程

##### routes/api.php 路由文件，写个路由便于测试
```
<?php

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Events\Wang;

Route::prefix('v2')->name('api.v2.')->group(function() {
    Route::get('version', function() {
        return 'this is version v2';
    })->name('version');
    Route::get('wang', function() {
        try {
            event(new Wang(123456));//触发事件
        } catch (Exception $exception){
            return 'this is error:'. $exception->getMessage();
        }
        return 'this is wang';
    })->name('version');
});
```
##### App/Events/Wang.php 事件
```
<?php

namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class Wang
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $socket;
    /**
     * Create a new event instance.
     *
     * @return void
     */
    public function __construct($data)
    {
        $this->socket = $data;//将数据塞到一个累变量里，方便让监听器获取
        error_log(date('Y-m-d H:i:s') . ' wang data :' . var_export($data,1) . "\n" ,3 , '/www/wwwroot/wang.txt');//记录日志用，证明跑到这里及打印出变量值，文件路径名记的要改成自己系统的
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn()
    {
        return new PrivateChannel('channel-name');
    }
}

```
##### App/Events/Yong.php 监听器
```
<?php

namespace App\Listeners;

use App\Events\Wang;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Exception;

class Yong
{
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct()
    {
    }

    /**
     * Handle the event.
     *
     * @param  \App\Events\Wang  $event
     * @return void
     */
    public function handle(Wang $event)
    {
        throw new Exception('尼玛无了,啊哈哈哈哈哈哈哈', 500);//用于 try catch 捕捉错误
        error_log(date('Y-m-d H:i:s') . ' yong handle :' . var_export($event,1) . "\n" ,3 , '/www/wwwroot/wang.txt');//记录日志用，证明跑到这里及打印出变量值，文件路径名记的要改成自己系统的
    }
}
```
##### postman 打接口进行验证
请求地址：http://larabbs.test/api/v2/wang
header参数 ：Accept->application/json











