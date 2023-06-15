---
title: "快速开始"
linkTitle: "快速开始"
weight: 2
---

## 概述

Macula快速开始内容说明主要包括两部分：一是Macula平台搭建，二是Macula平台接入。

## Macula平台搭建

### 环境准备

需要准备好JDK、Maven、Node.js、MySQL、Redis、Nacos，IDE建议使用idea，相关版本信息如下：

|  软件   | 版本  | 说明  |
|  ----  | ----  | ----  |
|  JDK   | 1.8以上  |   |

### 项目搭建

 1 JDK安装

 2 Node.js安装

 3 克隆项目源码
   
    后台代码：git clone https://github.com/macula-projects/macula-cloud.git
   
    前端代码：git clone https://github.com/macula-projects/macula-cloud-admin.git

 4 配置maven
    
  + 配置本地仓库路径、中央仓库镜像
  + 修改IDE的maven配置
  + maven自动导包设置
  
### 项目运行

1 打包并运行前端代码
   
      完成打包运行后，打开浏览器，访问前端配置的网址，如http://localhost:5800/，成功进入登录页面，即前端启动成功。

2 修改macula-could相关服务（macula-cloud-gateway、macula-cloud-system、macula-cloud-iam）的配置文件
      
      找到各服务对应的bootstrap.yml配置文件，对相关配置项进行修改。
      
3 运行macula-cloud相关服务（macula-cloud-gateway、macula-cloud-system、macula-cloud-iam）
 
      启动运行macula-cloud-gateway、macula-cloud-system、macula-cloud-iam（nacos配置的本机地址则要先启动nacos）。打开前端网站，点击登录，成功进入后台管理界面，即后端启动成功（账号：admin，密码：admin）

## Macula平台接入
