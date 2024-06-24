title: 日常问题排查
author: Laiyong Wang
tags: []
categories: []
date: 2022-11-02 21:05:00
---
## 死锁

### 操作步骤

1. **检测死锁**：使用 `SHOW ENGINE INNODB STATUS` 或查看错误日志。

    ```sql
    SHOW ENGINE INNODB STATUS\G
    ```

    或者：

    ```sh
    tail -f /var/log/mysql/error.log
    ```

2. **分析死锁信息**：在错误日志或 InnoDB 状态输出中找到相关的死锁信息，分析涉及的 SQL 语句和事务。

3. **调整应用程序和数据库**：
    - **优化事务**：尽量减少事务的复杂度和执行时间。
    - **索引优化**：确保关键查询使用合适的索引，避免全表扫描。

4. **实现重试机制**：

    ```php
    // PHP 代码示例：死锁重试机制
    function executeWithRetries($pdo, $query) {
        $maxRetries = 3;
        for ($i = 0; $i < $maxRetries; $i++) {
            try {
                $stmt = $pdo->prepare($query);
                $stmt->execute();
                return true;
            } catch (PDOException $e) {
                if (strpos($e->getMessage(), 'Deadlock found') !== false) {
                    // 死锁错误，重试
                    sleep($i + 1);
                } else {
                    // 非死锁错误，抛出异常
                    throw $e;
                }
            }
        }
        return false;
    }
    ```

5. **监控和调整**：使用监控工具（如 PMM、Nagios 或 Zabbix）持续监控数据库的死锁情况，并根据监控结果进行调整。

##  504


### 操作步骤

- 检查网络连接，先确定是单链接504还是整站
- 检查网关服务器配置
- 检查服务器性能是否能够支持当前的请求量
- 检查第三方接口是否超时
- 数据库查询是否太慢

### 解决方案
- 网络问题找服务商，
- 网关配置错了就改，能优化就优化
- 服务器负载，全高那就加服务器，单个高那就改 nginx 负载策略
- 第三方超时：能异步就异步，不能异步那给他设置超时限制，而且设置熔断策略，防止大量请求疯狂打入，搞崩服务器
- 优化sql、优化缓存、优化请求、使用cdn
- 实时监控，有问题最好先于用户发现
##  消息队列消息堆积

### 操作步骤
- 消费者是不是挂了
- 生产者发送消息的频率快不快
- 消费者的消费速度快不快，为什么慢
### 解决方案
- 堆积消息无用那就直接删
- 看情况可临时禁止生产者
- 增加消费速度
  - 优化下代码，慢慢跑（适合不急的）
  - 多线程处理（适合不要求消费顺序的）
  - 增加消费者脚本数量（适合不要求消费顺序的）



