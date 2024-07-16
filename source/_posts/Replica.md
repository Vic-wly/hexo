title: mongodb的副本集部署和分片集群部署
author: Laiyong Wang
date: 2024-07-15 19:21:00
tags:
---
MongoDB 提供了两种主要的高可用和扩展机制：副本集（Replica Set）和分片（Sharding）。下面分别介绍这两种机制的部署方法。

### 副本集部署

副本集是 MongoDB 提供的一种高可用性机制，通过在多个服务器上复制数据，确保数据的冗余和容灾能力。一个副本集包含一个主节点（Primary）和多个从节点（Secondary）。

#### 副本集部署步骤

1. **配置文件准备**

   为每个 MongoDB 实例创建一个配置文件。例如，`mongo1.conf`、`mongo2.conf`、`mongo3.conf`。每个配置文件内容如下：

   ```yaml
   # mongo1.conf
   systemLog:
     destination: file
     path: /var/log/mongodb/mongo1.log
     logAppend: true
   storage:
     dbPath: /var/lib/mongo1
   net:
     bindIp: localhost,<IP_ADDRESS>
     port: 27017
   replication:
     replSetName: rs0
   ```

2. **启动 MongoDB 实例**

   使用配置文件启动 MongoDB 实例：

   ```bash
   mongod --config /path/to/mongo1.conf
   mongod --config /path/to/mongo2.conf
   mongod --config /path/to/mongo3.conf
   ```

3. **初始化副本集**

   连接到一个 MongoDB 实例并初始化副本集：

   ```bash
   mongo --port 27017
   ```

   执行以下命令：

   ```javascript
   rs.initiate({
     _id: "rs0",
     members: [
       { _id: 0, host: "<IP_ADDRESS>:27017" },
       { _id: 1, host: "<IP_ADDRESS>:27018" },
       { _id: 2, host: "<IP_ADDRESS>:27019" }
     ]
   });
   ```

4. **验证副本集状态**

   使用以下命令查看副本集状态：

   ```javascript
   rs.status();
   ```

### 分片集群部署

分片是 MongoDB 提供的横向扩展机制，将数据分布到多个服务器上，支持大规模数据存储和查询。

#### 分片集群部署步骤

1. **配置服务器类型**

   分片集群由三个主要组件组成：配置服务器（Config Servers），分片服务器（Shard Servers），和查询路由器（Mongos）。

2. **启动配置服务器**

   创建配置文件 `configsvr.conf`：

   ```yaml
   systemLog:
     destination: file
     path: /var/log/mongodb/configsvr.log
     logAppend: true
   storage:
     dbPath: /var/lib/configsvr
   net:
     bindIp: localhost,<IP_ADDRESS>
     port: 27019
   sharding:
     clusterRole: configsvr
   ```

   启动配置服务器：

   ```bash
   mongod --config /path/to/configsvr.conf
   ```

3. **启动分片服务器**

   为每个分片创建配置文件 `shard1.conf`、`shard2.conf` 等：

   ```yaml
   # shard1.conf
   systemLog:
     destination: file
     path: /var/log/mongodb/shard1.log
     logAppend: true
   storage:
     dbPath: /var/lib/shard1
   net:
     bindIp: localhost,<IP_ADDRESS>
     port: 27018
   sharding:
     clusterRole: shardsvr
   ```

   启动分片服务器：

   ```bash
   mongod --config /path/to/shard1.conf
   mongod --config /path/to/shard2.conf
   ```

4. **启动查询路由器**

   创建查询路由器配置文件 `mongos.conf`：

   ```yaml
   systemLog:
     destination: file
     path: /var/log/mongodb/mongos.log
     logAppend: true
   net:
     bindIp: localhost,<IP_ADDRESS>
     port: 27017
   sharding:
     configDB: <CONFIG_SERVER_IP>:27019
   ```

   启动查询路由器：

   ```bash
   mongos --config /path/to/mongos.conf
   ```

5. **配置分片集群**

   连接到查询路由器：

   ```bash
   mongo --port 27017
   ```

   添加分片：

   ```javascript
   sh.addShard("<SHARD1_IP>:27018");
   sh.addShard("<SHARD2_IP>:27018");
   ```

6. **启用分片**

   为特定数据库启用分片：

   ```javascript
   sh.enableSharding("mydatabase");
   ```

   为特定集合启用分片并指定分片键：

   ```javascript
   sh.shardCollection("mydatabase.mycollection", { shardKey: 1 });
   ```

### 总结

- **副本集** 提供高可用性和容灾能力，通过数据复制保证数据的冗余。
- **分片集群** 提供横向扩展能力，通过数据分片支持大规模数据存储和查询。

正确地部署和配置副本集和分片集群，可以显著提高 MongoDB 系统的性能、可用性和可扩展性。