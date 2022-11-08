---
title: "ZKV对后端的浅尝辄止"
description: "凑活着瞎学学"
date: 2022-07-04T17:47:35+08:00
categories: "课堂笔记"
tags: [ "Develop" ]
image: "https://pic4.zhimg.com/v2-3ec9f9b98b4c3596e09b40d143410f0e_1440w.jpg?source=172ae18b"
---



TODO：

- oauth
  - [https://oauth.net/](https://oauth.net/)
- s3-pika
- restful
  - [https://restfulapi.net/](https://restfulapi.net/)

# 概念

## 总览

- [https://www.redhat.com/zh/topics](https://www.redhat.com/zh/topics)

## 云原生

- [https://www.redhat.com/zh/topics/cloud-native-apps](https://www.redhat.com/zh/topics/cloud-native-apps)

## 分布式

### CAP理论

在计算机科学中, CAP定理（CAP theorem）, 又被称作 布鲁尔定理（Brewer's theorem）, 它指出对于一个分布式计算系统来说，不可能同时满足以下三点:

- **一致性(Consistency)** (所有节点在同一时间具有相同的数据)
- **可用性(Availability)** (保证每个请求不管成功或者失败都有响应)
- **分隔容忍(Partition tolerance)** (系统中任意信息的丢失或失败不会影响系统的继续运作)

CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，最多只能同时较好的满足两个。

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类：

- CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。
- CP - 满足一致性，分区容忍性的系统，通常性能不是特别高。
- AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。

![img](https://www.runoob.com/wp-content/uploads/2013/10/cap-theoram-image.png)



# 编码

## Go语言

官网：[https://go.dev/](https://go.dev/)

语法：[https://go.dev/ref/spec](https://go.dev/ref/spec)

# 计算

## 技术

### LVS

- [https://cloud.tencent.com/developer/article/1657962](https://cloud.tencent.com/developer/article/1657962)
- [https://my.oschina.net/leeypp1/blog/294807?fromerr=xfCehUJY](https://my.oschina.net/leeypp1/blog/294807?fromerr=xfCehUJY)

## 产品

### CentOS

reference：

- [https://en.wikipedia.org/wiki/CentOS](https://en.wikipedia.org/wiki/CentOS)
- [https://zhuanlan.zhihu.com/p/337075432](https://zhuanlan.zhihu.com/p/337075432)

### Docker

#### dockerfile

- [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)
- [https://www.runoob.com/docker/docker-dockerfile.html](https://www.runoob.com/docker/docker-dockerfile.html)

#### compose

- [https://docs.docker.com/compose/](https://docs.docker.com/compose/)
- [https://www.runoob.com/docker/docker-compose.html](https://www.runoob.com/docker/docker-compose.html)

#### swarm

- [https://docs.docker.com/engine/swarm/](https://docs.docker.com/engine/swarm/)
- [https://www.runoob.com/docker/docker-swarm.html](https://www.runoob.com/docker/docker-swarm.html)

### K8s

[Kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/), also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

- [https://kubernetes.io/](https://kubernetes.io/)
- [https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)
- [https://www.redhat.com/zh/topics/containers/what-is-kubernetes](https://www.redhat.com/zh/topics/containers/what-is-kubernetes)



# 存储

## 技术

### SQL

[https://en.wikipedia.org/wiki/SQL](https://en.wikipedia.org/wiki/SQL)

### RAID

reference：

- [https://zh.wikipedia.org/zh-tw/RAID](https://zh.wikipedia.org/zh-tw/RAID)

### XFS

reference：

- [https://zh.wikipedia.org/zh-tw/XFS](https://zh.wikipedia.org/zh-tw/XFS)

### NoSQL

- [https://www.runoob.com/mongodb/nosql.html](https://www.runoob.com/mongodb/nosql.html)

## 产品

### Amazon S3

官网：[https://aws.amazon.com/cn/s3/](https://aws.amazon.com/cn/s3/)

Amazon Simple Storage Service (Amazon S3) 是一种对象存储服务

![img](https://d1.awsstatic.com/s3-pdp-redesign/product-page-diagram_Amazon-S3_HIW%402x.ee85671fe5c9ccc2ee5c5352a769d7b03d7c0f16.png)

Reference:

- [https://zhuanlan.zhihu.com/p/269794747](https://zhuanlan.zhihu.com/p/269794747)

### MongoDB

- [https://www.runoob.com/mongodb/mongodb-tutorial.html](https://www.runoob.com/mongodb/mongodb-tutorial.html)

### elasticsearch

[https://www.elastic.co/cn/](https://www.elastic.co/cn/)

- [https://github.com/elastic/elasticsearch](https://github.com/elastic/elasticsearch)
- [https://www.elastic.co/cn/elasticsearch/](https://www.elastic.co/cn/elasticsearch/)

#### Kibana

[https://www.elastic.co/cn/kibana/](https://www.elastic.co/cn/kibana/)

### Redis

- [https://redis.io/docs/about/](https://redis.io/docs/about/)
- [https://www.runoob.com/redis/redis-tutorial.html](https://www.runoob.com/redis/redis-tutorial.html)



# 管理

## 产品

### Apache Hadoop

- [https://hadoop.apache.org/](https://hadoop.apache.org/)
- [https://www.runoob.com/w3cnote/hadoop-tutorial.html](https://www.runoob.com/w3cnote/hadoop-tutorial.html)

### Apache Zookeeper

- [https://www.runoob.com/w3cnote/zookeeper-tutorial.html](https://www.runoob.com/w3cnote/zookeeper-tutorial.html)

### Apache Kafka

- [https://www.redhat.com/zh/topics/integration/what-is-apache-kafka](https://www.redhat.com/zh/topics/integration/what-is-apache-kafka)

### Jenkins

- [https://www.jenkins.io/](https://www.jenkins.io/)
- [https://www.liaoxuefeng.com/article/1083282007018592](https://www.liaoxuefeng.com/article/1083282007018592)
