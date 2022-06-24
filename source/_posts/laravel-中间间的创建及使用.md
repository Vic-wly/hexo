title: laravel 中间件的创建及使用
author: Laiyong Wang
date: 2022-01-22 17:30:45
tags:
---
PS：简单介绍下

##### 创建中间件
```
php artisan make:middleware WangLaiYong
```

##### 文件及内容
app/Http/Middleware/WangLaiYong.php
```
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class WangLaiYong
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle(Request $request, Closure $next)
    {
        $request->desc = '我是大帅逼';
        return $next($request);
    }
}

```
##### 使用：在 api 请求时使用
app/Http/Kernel.php
```
    protected $middlewareGroups = [
        'web' => [
       .
       .
       .
        ],
        'api' => [
            \App\Http\Middleware\WangLaiYong::class,//添加这行
            'throttle:60,1',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];
```