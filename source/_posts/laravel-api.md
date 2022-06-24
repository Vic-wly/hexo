title: laravel-api.php文件编辑及路由访问
author: Laiyong Wang
date: 2022-02-09 17:04:17
tags:
---
##### laravel 自带机制
###### api.php
```
<?php

use Illuminate\Support\Facades\Route;

Route::prefix('channelStorage/v2')->name('api.asd.')->group(function() {
    Route::get('wang', function() {
        return 'this is wang';
    })->name('wang');
});
```

###### 路由访问地址
```
// channelStorage/v2 是 prefix 中的内容，可以随便写，用作版本控制
${APP_URL}/api/channelStorage/v2/wang
```

##### dingo 
###### api.php
```
<?php

use Dingo\Api\Routing\Router;

$api = app(Router::class);
$api->version('v2', [
    'prefix' => 'api/channelStorage',
    'middleware' => [
//        'auth:sanctum', // 基于 sanctum 的 Authorization
        'throttle:60,1' // 控制接口访问频率
    ],
], function (Router $api): void {
    $api->get('asd', function (){
        return 123;
    });
});

```
###### 配置
```
APP_DOMAIN=icsp.test
APP_URL="http://${APP_DOMAIN}"

API_DOMAIN="${APP_DOMAIN}" //主要是这个，上面两个是laravel里的
```
###### 路由访问地址
```
// api/channelStorage 是 prefix 中的内容，可以随便写，用作版本控制
${APP_URL}/api/channelStorage/wang
// header 需要传的键与值
Accept : application/x..v2+json
```