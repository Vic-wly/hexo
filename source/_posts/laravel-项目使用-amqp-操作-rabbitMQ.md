title: laravel 项目使用 amqp 操作 rabbitMQ
author: Laiyong Wang
date: 2022-01-07 14:35:07
tags:
---
##### env里配置环境变量
```bash
//AMQP_** 就是rabbitMQ的配置
RABBITMQ_QUEUE=queue_name //名字
RABBITMQ_HOST="${AMQP_HOST}"
RABBITMQ_PORT="${AMQP_PORT}"
RABBITMQ_USER="${AMQP_USER}"
RABBITMQ_PASSWORD="${AMQP_PASSWORD}"
RABBITMQ_VHOST="${AMQP_VHOST}"
RABBITMQ_FAILED_EXCHANGE=exchange_name  //名字
RABBITMQ_FAILED_ROUTING_KEY=routinf_key_name  //名字
RABBITMQ_SSL_CAFILE=
RABBITMQ_SSL_LOCALCERT=
RABBITMQ_SSL_LOCALKEY=
RABBITMQ_SSL_VERIFY_PEER=true
RABBITMQ_SSL_PASSPHRASE=
RABBITMQ_WORKER=default
```

##### 创建一个job

```bash
php artisan module:make:job Testjob Test //这个命令是模块化内创建
php artisan make:job Testjob  //正常创建
```
```
<?php

namespace Modules\Crm\Jobs\Nlb;

use Bschmitt\Amqp\Facades\Amqp;
use Exception;
use Illuminate\Bus\Queueable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;
use Modules\Crm\Queue\RabbitMQJob;
use Xpx\Eloquent\Models\AdminUser;
use Xpx\Eloquent\Models\User;

class UpdateSaleJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;


    /**
     * Create a new job instance.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Execute the job.
     *
     * @param RabbitMQJob $job
     * @param array $data
     * @throws \Illuminate\Contracts\Container\BindingResolutionException
     */
    public function handle(RabbitMQJob $job, array $data)
    {
        try {
            Log::info('CRM_NLB-MQ-CONSUME', $data);
            if (true) {
                $messageId = $data['mc']['message_id'];
                $globalUserId = $data['data']['global_user_id'];
                $source = $data['data']['sale_user'][0]['source'];
                $salesGlobalUserId = $data['data']['sale_user'][0]['sale_user_id'];
                Log::info('CRM_NLB-MQ-PROCESSING', compact('messageId', 'globalUserId', 'salesGlobalUserId', 'source'));
                $queue = config('crm.nlb.mq.queue');
                $exchange = config('crm.nlb.mq.exchange');
                $exchange_type = 'direct';
                $routingKey = config('crm.nlb.mq.routing.key');
                $userName = '';
                $salesUserName = '';
                $message = compact('globalUserId', 'userName', 'salesGlobalUserId', 'salesUserName');
                Amqp::publish($routingKey, json_encode($message), compact('queue', 'exchange', 'exchange_type'));
                Log::info('CRM_NLB-MQ-PUBLISH', compact('queue', 'exchange', 'exchange_type', 'routingKey', 'message'));
            }
        } catch (Exception $e) {
            Log::error('CRM_NLB-MQ-ERROR -> ' . $e->getMessage(), $e->getTrace());
        } finally {
            if ($messageId = $data['mc']['message_id'] ?? false) {
                $url = config('crm.mc.url');
                $response = Http::post(sprintf('%s/handleFinish', $url), [
                    'message_id' => $messageId,
                ]);
                $successful = $response->successful();
                $status = $response->status();
                $body = $response->body();
                Log::info('CRM_NLB-MC-FINISH', compact('successful', 'url', 'messageId', 'status', 'body'));
            }
            $job->delete();
            Log::info('CRM_NLB-MQ-DELETE', [$job->getRabbitMQMessage()->getDeliveryTag()]);
        }
    }
}

```

##### 创建一个消费队列？

```bash
//文件名 modules/Crm/Queue/Nlb/RabbitMQJob.php 
<?php

namespace Modules\Crm\Queue\Nlb;

use Modules\Crm\Jobs\Nlb\UpdateSaleJob;
use VladimirYuldashev\LaravelQueueRabbitMQ\Queue\Jobs\RabbitMQJob as BaseJob;

class RabbitMQJob extends BaseJob
{

    public function payload(): array
    {
        return [
            'job'  => UpdateSaleJob::class . '@handle',
            'data' => json_decode($this->getRawBody(), true),
        ];
    }
}
```
##### config/queue.php 内参数 connections 新增内容

```bash

        'crm_nlb-rabbitmq' => [

            'driver' => 'rabbitmq',
            'queue' => env('RABBITMQ_QUEUE', 'default'),
            'connection' => PhpAmqpLib\Connection\AMQPLazyConnection::class,

            'hosts' => [
                [
                    'host' => env('RABBITMQ_HOST', '127.0.0.1'),
                    'port' => env('RABBITMQ_PORT', 5672),
                    'user' => env('RABBITMQ_USER', 'guest'),
                    'password' => env('RABBITMQ_PASSWORD', 'guest'),
                    'vhost' => env('RABBITMQ_VHOST', '/'),
                ],
            ],

            'options' => [
                'ssl_options' => [
                    'cafile' => env('RABBITMQ_SSL_CAFILE', null),
                    'local_cert' => env('RABBITMQ_SSL_LOCALCERT', null),
                    'local_key' => env('RABBITMQ_SSL_LOCALKEY', null),
                    'verify_peer' => env('RABBITMQ_SSL_VERIFY_PEER', true),
                    'passphrase' => env('RABBITMQ_SSL_PASSPHRASE', null),
                ],
                'queue' => [
                    'job' => Modules\Crm\Queue\Nlb\RabbitMQJob::class,  //第四步创建的
                    'reroute_failed' => true,
                    'failed_exchange' => env('RABBITMQ_FAILED_EXCHANGE'),
                    'failed_routing_key' => env('RABBITMQ_FAILED_ROUTING_KEY'),
                ],
            ],

            /*
             * Set to "horizon" if you wish to use Laravel Horizon.
             */
            'worker' => env('NLB_RABBITMQ_WORKER', 'default'),

        ],

```

##### 监听运行
```
php artisan queue:work crm_nlb-rabbitmq //config/queue.php 内参数 connections 新增的内容
```
