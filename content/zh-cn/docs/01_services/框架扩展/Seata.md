---
title: "Macula Boot Starter Seata"
linkTitle: "分布式事务"
weight: 12
---

接入Seata的配置模块

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

默认支持RestTemplate、FeignClient、Dubbo RPC