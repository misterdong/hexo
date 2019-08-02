---
title: redis学习之旅
date: 2019-07-23 18:27:04
tags: redis
categories: redis
---

#### redis过期时间 

消极方法（passive way）：在访问key时，如果发现过期，则进行删除 

积极方法（active way）：周期性的从设置了过期时间的key中选择一部分的key进行删除 

1. 随机测试20个带有timeout信息的key进行测试 

2. 删除所有过期的key 

3. 如果超过25%的key被删除，则重复执行步骤1 

<!-- more -->

#### 持久化 

##### RDB 

当符合【条件】条件的时候，fork子进程，把数据写到dump.rdb快照文件，不会对主进程操作进行影响 

条件: 

配置规则，config文件 

save seconds changes 

save 900 1 

save 300 10 

save 60 10000 

save，会阻塞所有客户端的请求；bgsave，异步存储，不会阻塞 

flushall，配置规则存在的情况下，执行一次快照 

执行复制操作 

缺点：数据丢失 

##### AOF 

开启方式：redis.conf-> append-only yes 

> AOF 开启的情况下，默认从AOF中读取数据 

#### Redis的内存回收策略

LRU

maxmemory-policy  ：

- noeviction(默认)
- allkeys-lru 最少使用的数据淘汰
- allkeys-random 随机移除某些数据
- volatile-random/lru/ttl 从已经设置的过期时间的数据中随机淘汰/淘汰最少使用数据/淘汰即将过期的数据

#### Redis单线程为什么性能高

采用多路复用机制

#### Lua脚本在Redis中的应用

1. 在应用中需要保证多个命令保证原子性，需要时用Lua脚本进行处理
2. 在使用pipeline管道模型时，减少网络开销取执行多个命令
3. 复用性

在lua脚本中执行redis的命令

redis.call('set','lua','value')

local val = redis.call('get','lua')

编写lua脚本文件，如demo.lua

```lua
return redis.call('get','lua')
```

执行：

```shell
./redis-cli --eval demo.lua
```

例：基于lua脚本实现ip限流

便携lua文件 ratelimit.lua

```lua
local key="ratelimit:"..KEYS[1]
local limit=tonumber(ARGV[1])
local expireTime=ARGV[2]
local times = redis.call('incr',key)
if times == 1 then
  redis.call('expire',key,expireTime)
end
if times > limit then
  return 0
end
return 1
```

执行：

```shell
./redis-cli --eval ratelimit.lua 192.167.0.2,10 10
```

