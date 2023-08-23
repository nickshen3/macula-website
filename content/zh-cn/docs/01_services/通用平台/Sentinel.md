---
title: "服务治理(Sentinel)"
linkTitle: "服务治理(Sentinel)"
weight: 20
---

## 概述

macula-cloud-sentinel基于官方的sentinel-dashboard并引入nacos作为数据源的服务治理管理平台。在macula-boot-starter-cloud-alibaba中已经引入了sentinel依赖包。只需通过配置打开即可。



## 使用配置

```yaml
spring:
	sentinel:
      #启动后马上初始化，而不是等有流量有再初始化。否则会提示：Runtime port not initialized, won't send heartbeat
      eager: true
      transport:
        # 控制台地址
        dashboard: localhost:8080
        # 客户端监控API的端口，默认8719，与Sentinel控制台做交互。有规则变化会把规则数据push给这个Http Server接收,然后注册到sentinel中
        port: 8719
      # Sentinel Nacos数据源配置，Nacos中的规则会⾃动同步到sentinel控制台的流控规则中
      # com.alibaba.cloud.sentinel.SentinelProperties.datasource
      # 配置了数据源后，在nacos修改中会自动同步到sentinel
      # ⾃定义数据源名,随意不重复即可；可多个
      # The following values are valid:
      # AUTHORITY,DEGRADE,FLOW,GW_API_GROUP,GW_FLOW,PARAM_FLOW,SYSTEM
      datasource:
        flow:
          # 指定数据源类型
          nacos:
            server-addr: 127.0.0.1:8848
            data-id: ${spring.application.name}-flow-rules
            namespace: SENTINEL
            # 默认分组：DEFAULT_GROUP
            #group-id: SENTINEL_GROUP
            data-type: json
            rule-type: flow
        degrade:
          nacos:
            server-addr: 127.0.0.1:8848
            data-id: ${spring.application.name}-degrade-rules
            namespace: SENTINEL
            # 默认分组：DEFAULT_GROUP
            #group-id: SENTINEL_GROUP
            data-type: json
            rule-type: degrade
```



## 客户端使用





## 服务端介绍



## 版权说明