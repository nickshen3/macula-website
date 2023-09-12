---
title: "应用分层"
linkTitle: "应用分层"
weight: 5
---

## 概述

关于应用的分层架构，长期以来使用最广的就是经典三层架构（Controller层，Service层，Mapper/Dao层），近几年DDD的架构逐步兴起（API层、ApplicationService层、DomainService层和Infrastructure层）。以下主要对经典三层架构及层模型对象进行说明（Macula没有强制使用哪种分层架构，看具体的场景和团队情况选择适合的分层架构即可）。

## 经典三层架构

### 架构图

![image](../images/3layer.png)


### 对象模型说明(Query,Form,DTO,VO,BO,Entity)

![image](../images/layer-model.png)
