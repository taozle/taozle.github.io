---
layout: post
title: Moving persistent data out of Redis
date: 2017-3-18 19:20
categories: Weekly
tags: Redis Github
---

http://githubengineering.com/moving-persistent-data-out-of-redis/

下面的 MySQL 指的是 Github 自己改造的 `MySQL::KV`

### 为什么要把持久型数据从 Redis 移到 MySQL

- 减少运维的成本
- 我们 MySQL 的经验很足
- 性能提升。消除在持久化大量数据到硬盘上时的 IO 延迟

### 最大的挑战：迁移 Activity Feeds

原来的做法是把 Redis 当做二级索引。发生一个事件（点赞、关闭 issue、上传 commit 等）的时候，把事件本身存到 MySQL 中，然后把事件的标示存到关心的用户的 Key 下面（ 推模型）。这是个大量写的场景，不能简单的用 MySQL 来替换（MySQL 扛不住）。

简单来说，迁移到 MySQL 之前我们把原来的推模型改成了推拉结合的模型。

### 结论

先测量，在迁移。
