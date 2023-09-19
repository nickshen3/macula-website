---
title: "应用分层"
linkTitle: "应用分层"
weight: 5
---

## 概述

关于应用的分层架构，长期以来使用最广的就是经典三层架构（UI展示层，BLL业务逻辑层，DAL数据访问层），近几年DDD的架构逐步兴起（接口层、应用服务层、领域服务层和基础设施层）。以下主要对经典三层架构及其模型对象进行说明（Macula没有强制使用哪种分层架构，看具体的场景和团队情况选择适合的分层架构即可）。

## 经典三层架构

### 架构图

经典三层架构如下图所示：

![image](../images/model-small.png)

### 对象模型说明(Query,Form,DTO,VO,BO,Entity)

业内涉及分层架构的模型对象多种多样，如VO、DTO、AO、BO、PO、DO、Entity等，但大家的理解和用法各异。在此，Macula选取以下模型并明确定义其使用场景，如下图所示：

![image](../images/layer-model.png)

+ Query查询对象：查询的时候使用，可以透传Controller，Service，Mapper三层
+ Form表单对象和DTO传输对象：更新的时候使用
+ VO视图对象：返回结果的时候使用
+ BO业务对象：由SQL联合多表查询返回的对象，或者由多个Entity组合的对象
+ Entity实体对象：与数据库表一一对应的实体对象，与PO等价

以上模型对象的定义和使用，同样适用于平台内部微服务间的调用以及跨平台的openapi交互场景。如下图所示：

![image](../images/model2.png)

详细样例请看：https://github.com/macula-projects/macula-samples
