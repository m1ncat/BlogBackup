---
title: redis集群部署
date: 2017-04-21 14:42:30
tags:
- redis cluster
categories:
- redis

---

最近写的项目用到了redis数据库，而redis在3.0版本后支持了redis集群部署，所以考虑在自己的项目里面使用redis集群存储数据。现在把部署redis集群的过程记录一下，以备不时之需。

我是在两台云主机`114.80.212.194` `118.119.254.205`上部署redis集群的，最后部署的命令中会替换成你自己的ip。

<!--more-->

1. 安装最新版本的redis(仅供参考，redis版本可能会不一样)

   ```shell
   cd /home/soft
   wget http://download.redis.io/releases/redis-3.2.8.tar.gz
   tar xzvf redis-3.2.8.tar.gz
   cd redis-3.2.8
   make 
   make install  
   ```

2. 创建集群所需目录结构(7000, 7001, 7002是redis集群部署的接口)

   ```
   cd /home/soft
   mkdir cluster
   mkdir 7000 7001 7002
   ```

3. 修改redis.conf

   ```
   cp /home/soft/redis-3.2.8/redis.conf /home/soft/cluster
   ```

   redis.conf修改内容

   ```
   port 7000 (下一步这个地方需要随着端口修改)
   cluster-enabled yes
   cluster-config-file nodes-7000.conf
   cluster-node-timeout 5000
   appendonly yes
   bind 114.80.212.194 (部署机器的ip)
   ```

   复制

   ```
   cp /home/soft/cluster/redis.conf /home/soft/cluster/7000/redis.conf
   cp /home/soft/cluster/redis.conf /home/soft/cluster/7001/redis.conf
   cp /home/soft/cluster/redis.conf /home/soft/cluster/7002/redis.conf
   ```

4. 启动redis server

   ```
   redis-server /home/soft/cluster/7000/redis.conf
   redis-server /home/soft/cluster/7001/redis.conf
   redis-server /home/soft/cluster/7002/redis.conf
   ```

5. 部署

   ```
   cd /home/soft/redis-3.2.8/src
    ./redis-trib.rb create --replicas 1 114.80.212.194:7000 114.80.212.194:7001 114.80.212.194:7002 118.119.254.205:7000 118.119.254.205:7001 118.119.254.205:7002
   ```

   ​