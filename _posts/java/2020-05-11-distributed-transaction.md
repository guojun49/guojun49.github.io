---
layout: post
title: 分布式事务解决方案
category: java
tags: [java]
excerpt: 分布式事务解决方案
---

## 一.分布式事务产生的场景
- 微服务之间通过远程调用完成事务操作
![](assets/images/2020/0E08GB7RLMS6ELBLRIXMQ5B.png)

- 单体系统访问多个数据库实例
![](assets/images/2020/DP(U{JH0Z7%{UNNYED7Y{7J.png)

- 多服务访问同一个数据库实例
![](assets/images/2020/BN1365LL_54K_9(EX3R%__C.png)

## 二.分布式事务理论基础
### 2.1 CAP理论
CAP是Consistency、Availability、Partition tolerance三个词语的缩写，分别表示一致性、可用性、分区容忍
性。
![](assets/images/2020/FT([(H$JDP5V[6@Y959@SBW.png)
整体执行流程如下：
1、商品服务请求主数据库写入商品信息（添加商品、修改商品、删除商品）
2、主数据库向商品服务响应写入成功。
3、商品服务请求从数据库读取商品信息。


C - Consistency：
一致性是指写操作后的读操作可以读取到最新的数据状态，当数据分布在多个节点上，从任意结点读取到的数据都
是最新的状态。
分布式系统一致性的特点：
1、由于存在数据同步的过程，写操作的响应会有一定的延迟。
2、为了保证数据一致性会对资源暂时锁定，待数据同步完成释放锁定资源。
3、如果请求数据同步失败的结点则会返回错误信息，一定不会返回旧数据。

A - Availability ：
可用性是指任何事务操作都可以得到响应结果，且不会出现响应超时或响应错误。
分布式系统可用性的特点：
1、所有请求都有响应，且不会出现响应超时或响应错误。

P - Partition tolerance 
通常分布式系统的各各结点部署在不同的子网，这就是网络分区，不可避免的会出现由于网络问题而导致结点之间
通信失败，此时仍可对外提供服务，这叫分区容忍性。
分布式分区容忍性的特点：
1、分区容忍性分是布式系统具备的基本能力。

CAP是一个已经被证实的理论：一个分布式系统最多只能同时满足
一致性（Consistency）、可用性（Availability）和分区容忍性（Partition tolerance）这三项中的两项。它可以作
为我们进行架构设计、技术选型的考量标准。对于多数大型互联网应用的场景，结点众多、部署分散，而且现在的
集群规模越来越大，所以节点故障、网络故障是常态，而且要保证服务可用性达到N个9（99.99..%），并要达到良
好的响应性能来提高用户体验，因此一般都会做出如下选择：保证P和A，舍弃C强一致，保证最终一致性。


### 2.2 BASE理论
BASE 是Basically Available(基本可用)、Soft state(软状态)和Eventually consistent (最终一致性)三个短语的缩
写。BASE理论是对CAP中AP的一个扩展，通过牺牲强一致性来获得可用性，当出现故障允许部分不可用但要保证
核心功能可用，允许数据在一段时间内是不一致的，但最终达到一致状态。满足BASE理论的事务，我们称之为“柔
性事务”。

- 基本可用:分布式系统在出现故障时，允许损失部分可用功能，保证核心功能可用。如，电商网站交易付款出
现问题了，商品依然可以正常浏览。
- 软状态:由于不要求强一致性，所以BASE允许系统中存在中间状态（也叫软状态），这个状态不影响系统可用
性，如订单的"支付中"、“数据同步中”等状态，待数据最终一致后状态改为“成功”状态。
- 最终一致:最终一致是指经过一段时间后，所有节点数据都将会达到一致。如订单的"支付中"状态，最终会变
为“支付成功”或者"支付失败"，使订单状态与实际交易结果达成一致，但需要一定时间的延迟、等待。



#### 可靠消息一致性