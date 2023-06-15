---
title: "快速开始"
linkTitle: "快速开始"
weight: 2
---

## 概述

Macula快速开始内容主要包括两部分：一是Macula平台搭建，二是Macula平台接入。

## Macula平台搭建

### 环境准备

需要准备好JDK、Maven、Node.js、MySQL、Redis、Nacos，IDE建议使用idea，相关版本信息如下：

|  软件   | 版本  | 说明  |
|  ----  | ----  | ----  |
|  JDK   | 1.8以上  |   |
|  Maven   | 3.8.1以上  |   |
|  Node.js   | 14.18.3以上  |   |
|  MySQL   | 5.7.0以上  |   |
|  Redis   | 3.0.0以上  |   |
|  Nacos   | 2.0.0以上  |   |

### 项目搭建

 1 JDK安装

 2 Node.js安装

 3 克隆项目源码
   
    后台代码：git clone https://github.com/macula-projects/macula-cloud.git
   
    前端代码：git clone https://github.com/macula-projects/macula-cloud-admin.git

 4 配置maven
    
  + 配置本地仓库路径、中央仓库镜像
  + 修改IDE的maven配置
  
 5 Redis准备
 
    Redis理论上是需要准备两个实例，一个是Macula平台与各租户（应用平台）共享的实例，一个是Macula平台独享的实例（非生产环境，不区分也行）。
  
 6 MySQL准备
 
    MySQL准备好后，需要执行macula-cloud-system中的system.ddl建表语句，完成system系统管理的数据库表初始化。
  
### 项目运行

1 打包并运行前端代码
   
      完成打包运行后，打开浏览器，访问前端配置的网址，如http://localhost:5800/，成功进入登录页面，即前端启动成功。

2 修改macula-could相关服务（macula-cloud-gateway、macula-cloud-system、macula-cloud-iam）的配置文件
      
      找到各服务对应的bootstrap.yml配置文件，对相关配置项进行修改。
      
3 运行macula-cloud相关服务（macula-cloud-gateway、macula-cloud-system、macula-cloud-iam）
 
      启动运行macula-cloud-gateway、macula-cloud-system、macula-cloud-iam（nacos配置的本机地址则要先启动nacos）。打开前端网站，点击登录，成功进入后台管理界面，即后端启动成功（账号：admin 密码：admin）

## Macula平台接入

### Macula平台管理员创建用户、租户

    各应用平台（租户）想接入Macula平台，首先要联系Macula平台的管理员，在MaculaCloudAdmin后台为各应用平台创建对应的租户以及管理账号。

### Macula平台租户管理员创建应用

    各应用平台获得Macula平台的管理账号后，即可登录MaculaCloudAdmin后台，进行应用创建、菜单创建等操作。

### 租户（应用平台）接入Macula平台

1 **前端工程接入**

  此部分为两种情形：一是接入端使用自有前端代码进行接入，二是接入端使用 macula-cloud-admin前端代码。

 + 接入端使用自有平台的前端代码

   使用 macula-cloud-admin 提供的路由相关的js代码进行菜单渲染，如下图
   

 + 接入端使用 macula-cloud-admin 前端代码

  （1）执行如下命令，拉取 macula-cloud-admin 仓库 (SSH) 
   
      git clone https://github.com/macula-projects/macula-cloud-admin.git
      
  （2）进入项目目录，安装依赖
  
      npm i
      
  （3）修改.env.development 文件配置网关
  
      VITE_APP_PROXY = false
      VITE_APP_TITLE = MACULA(DEV)
      VITE_APP_API_BASEURL = http://localhost:9000
      
  （4）启动项目
  
      npm run dev
      
  （5）启动成功后，访问如下地址即可
  
      http://localhost:5800

   注意：安装依赖和启动需依靠npm、cnpm或者yarn

2 **后端工程接入**

应用平台后端工程接入Macula平台的基本步骤如下：

（1）接入端后端引入 macula-boot-starter-system

    <dependency>
        <groupId>dev.macula.boot</groupId>
        <artifactId>macula-boot-starter-system</artifactId>
    </dependency>
    
  macula-boot-starter-system 实际上只提供两个接口
  
 + 获取登录用户信息 - 【/api/v1/users/me】

       {
        // 用户ID
        "userId": 576,
        // 用户名
        "username": "0011633",
        // 用户昵称
        "nickname": "Ziv",
        // 头像地址
        "avatar": "https://s2.loli.net/2022/04/07/gw1L2Z5sPtS8GIl.gif",
        // 用户角色编码集合
        "roles": [
        "role::add"
        ],
        // 用户权限标识集合
        "perms": [
        "SCOPE_all", "ROOT", "ADMIN", "USER"
        ]
       }
   
 + 当前应用的菜单路由 - 【/api/v1/menus/routes】

       {
        "path": "/system",
        "meta": {
        "type": "CATALOG",
        "title": "系统管理",
        "icon": "el-icon-setting",
        "hidden": false,
        "alwaysShow": true,
        "roles": [
          "ADMIN",
          "ROOT",
          "GUEST",
          "USER"
        ],
        "keepAlive": true
        },
        "children": [
        {
          "path": "/system/log",
          "component": "system/log",
          "name": "/system/log",
          "meta": {
            "type": "MENU",
            "title": "审计日志",
            "icon": "el-icon-credit-card",
            "hidden": false,
            "alwaysShow": false,
            "roles": [
              "GUEST",
              "ADMIN",
              "ROOT"
            ],
            "keepAlive": true
          }
        }
        ]
       }
   
（2）使用 macula-cloud-system提供的应用AK、SK调用接口，需在yml 配置如下信息：

     macula:
       cloud:
        app-key: ecp                            # 接入到macula cloud的appkey
        secret-key: xxxxx                       # 接入到macula cloud的secretKey
        system:
          endpoint: http://localhost:9000/system  # macula cloud system的端点
          

至此，Macula平台的使用你已经快速启动起来了。如果遇到问题，也不用急躁，请联系平台负责人获取帮助。
