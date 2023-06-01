---
title: "Macula Boot Starter Seata"
linkTitle: "分布式事务"
weight: 12
---

## 概述

本模块提供基于seata分布式事务的接入功能。



## 组件坐标

```xml
<dependency>
    <groupId>dev.macula.boot</groupId>
    <artifactId>macula-boot-starter-seata</artifactId>
    <version>${macula.version}</version>
</dependency>
```



## 使用配置

客户端加入如下配置

```yaml
seata:
  config:
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      group : "SEATA_GROUP"
      namespace: ""
      username: "nacos"
      password: "nacos"
  registry:
    type: nacos
    nacos:
      application: seata-server
      server-addr: 127.0.0.1:8848
      group : "SEATA_GROUP"
      namespace: ""
      username: "nacos"
      password: "nacos"
```



## 核心功能

前提要部署seata-server，具体见[官方文档](https://seata.io/zh-cn/docs/ops/deploy-guide-beginner.html)

## 依赖引入

## 版权说明

接入Seata的配置模块

默认支持RestTemplate、FeignClient、Dubbo RPC