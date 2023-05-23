---
title: "概述"
linkTitle: "概述"
weight: 1
---
## 1 平台简介
Macula是一个微服务应用开发平台，主要包括三大部分：MaculaBoot、MaculaCloud和MaculaCloudAdmin。MaculaBoot是微服务应用开发所需要的开发框架（如SpringCloudAlibaba、SpringCloudTecent等），基于众多开源产品进行甄选后轻度封装而成。MaculaCloud是一个微服务架构的通用技术服务体系，一方面可集成对接各大云厂商的微服务治理体系（如腾讯云TSF、阿里云EDAS、百度云CNAP等），另一方面提供大量内置可复用的通用技术服务（如系统管理、消息推送、资源存储、ID生成、任务调度等）。MaculaCloudAdmin是基于VUE的前端项目，与MaculaCloud配套，提供管理界面功能。使用Macula进行微服务架构的应用平台开发，一方面可以统一技术栈，降低管理与维护成本；另一方面可以避免重复造轮子，提升开发效率。
## 2 整体架构
Macula的整体架构如下图所示：
![image](https://github.com/morganqhr/macula-website/blob/3d9381889018304457f23ffe7309c210b249c52b/static/img/structure-diagram.png)
## 3 主要功能
### 3.1 MaculaCloud
+ 统一网关：所有请求的入口，负责统一的认证、鉴权、路由、限流、降级
+ 统一认证：即IAM服务，提供多种认证协议（Oauth2、OIDC、SAML2、CAS等），集成多种身份源（企微、钉钉等）
+ 系统服务：包括菜单管理、角色管理、用户管理、租户管理、应用管理、客户端管理、字典管理与审计日志等通用系统功能
+ 消息推送：集成个微消息、企微消息、腾讯推送等消息渠道
+ 资源存储：集成七牛云存储、阿里云存储与腾讯云存储
+ ID生成：集成滴滴开源的TinyID服务
+ 任务调度：集成powerjob服务
+ 其他（如审批流、短链、数据源版本管理）
### 3.2 MauclaBoot
+ Feign：统一使用Feign进行HTTP远程调用
+ Lock4j：统一使用Lock4j进行分布式锁控制
+ Idempotent：统一使用Idempotent进行幂等控制
+ Seata：统一使用Seata进行分布式事务控制
+ SpringCloudGateway：统一使用SCG作为应用网关
+ SpringCloudAlibaba:阿里开源的SpringCloud版本（按需选择）
+ SpringCloudTecent：腾讯开源的SpringCloud版本（按需选择）
+ SpringCloudTsf：腾讯TSF需要依赖的包（按需选择）
+ 其他（Election、Statemachine、retry、mapstruct、mybatis、redis、web等）
## 4 技术原理
Macula整体的技术交互与应用原理如下图所示：

## 5 部署方案
### 5.1 虚拟机部署
TODO
### 5.2 容器云部署
TODO
