title: PHP 实现 redis 分布式锁
author: Laiyong Wang
tags:
  - 锁
  - php
  - redis
categories: []
date: 2024-06-15 14:37:00
---
### 代码

```
<?php

function acquireLock($redis, $lockKey, $lockValue, $expireTime) {
    // 使用 SET 命令获取锁，设置 NX 参数确保键在不存在时才会被设置，EX 参数设置过期时间
    return $redis->set($lockKey, $lockValue, array('nx', 'ex' => $expireTime));
}

function releaseLock($redis, $lockKey, $lockValue) {
    // 使用 Lua 脚本原子性地释放锁
    $script = '
        if redis.call("get", KEYS[1]) == ARGV[1] then
            return redis.call("del", KEYS[1])
        else
            return 0
        end
    ';
    return $redis->eval($script, array($lockKey, $lockValue), 1);
}

$redis = new Redis();
$redis->connect('127.0.0.1', 6379);

$lockKey = "wanglaiyong"; // 锁的键
$lockValue = uniqid(); // 锁的唯一值
$expireTime = 10; // 锁的过期时间，单位为秒

if (acquireLock($redis, $lockKey, $lockValue, $expireTime)) {
    echo "获取锁成功，正在执行代码...\n";
    
    // 执行需要加锁的代码
    sleep(5);

    if (releaseLock($redis, $lockKey, $lockValue)) {
        echo "释放锁成功。\n";
    } else {
        echo "释放锁失败。\n";
    }
} else {
    echo "获取锁失败。\n";
}
?>
```

### 总结

Redis 分布式锁通过 `SET` 命令的 NX 和 EX 参数实现获取锁的操作，确保只有在键不存在时才会设置键，并且设置键的过期时间以防止死锁。释放锁时，通过 Lua 脚本确保释放锁的操作是原子的，避免在高并发环境下发生竞态条件。

### lua 脚本
在释放的时候，处理的确实比自己的做法好，借鉴了