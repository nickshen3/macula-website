---
title: "概述"
linkTitle: "概述"
weight: 1
---
## 1 平台简介
Macula是一个微服务应用开发平台，主要包括三大部分：MaculaBoot、MaculaCloud和MaculaCloudAdmin。MaculaBoot是微服务应用开发所需要的开发框架（如SpringCloudAlibaba、SpringCloudTecent等），基于众多开源产品进行甄选与轻度封装而成。MaculaCloud是一个微服务架构的通用技术服务体系，一方面可集成对接各大云厂商的微服务治理体系（如腾讯云TSF、阿里云EDAS、百度云CNAP等），另一方面提供大量内置可复用的通用技术服务（如系统管理、消息推送、资源存储、ID生成、任务调度等）。MaculaCloudAdmin是基于VUE的前端项目，与MaculaCloud配套，提供管理界面功能。使用Macula进行微服务架构的应用平台开发，一方面可以统一技术栈，降低管理与维护成本；另一方面可以避免重复造轮子，提升开发效率。
## 2 整体架构
Macula的整体架构如下图所示：
![image](https://github.com/morganqhr/macula-website/blob/3d9381889018304457f23ffe7309c210b249c52b/static/img/structure-diagram.png)
## 3 主要功能
### 3.1 MaculaCloud
+ 统一网关
+ 统一认证
+ 系统服务
+ 消息推送
+ 资源存储
+ ID生成
+ 任务调度
### 3.2 MauclaBoot
+ Feign
+ SpringCloudGateway
+ SpringCloudAlibaba
+ SpringCloudTecent
+ SpringCloudTsf
+ Lock4j
## 4 技术原理
### 4.1 技术架构

### 4.2 交互说明

## 5 部署方案
### 5.1 虚拟机部署

### 5.2 容器云部署

