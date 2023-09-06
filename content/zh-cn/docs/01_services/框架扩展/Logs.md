---
title: "Macula Boot Starter Logs"
linkTitle: "日志与监控"
weight: 3
---

## 概述

本模块主要提供日志发送、日志审计、监控上报等功能，由多个子模块组成。包括：

- macula-boot-starter-auditlog  日志审计记录
- macula-boot-starter-logstash  将日志发送给logstash
- macula-boot-starter-skylog  将日志发送给skywalking
- macula-boot-starter-prometheus 监控数据上报给prometheus

## 组件坐标

```xml
<!-- 日志审计 -->
<dependency>
    <groupId>dev.macula.boot</groupId>
    <artifactId>macula-boot-starter-auditlog</artifactId>
    <version>${macula.version}</version>
</dependency>

<!-- 发送日志到logstash -->
<dependency>
    <groupId>dev.macula.boot</groupId>
    <artifactId>macula-boot-starter-logstash</artifactId>
    <version>${macula.version}</version>
</dependency>

<!-- 发送日志到skywalking -->
<dependency>
    <groupId>dev.macula.boot</groupId>
    <artifactId>macula-boot-starter-skylog</artifactId>
    <version>${macula.version}</version>
</dependency>

<!-- 发送监控指标到prometheus -->
<dependency>
    <groupId>dev.macula.boot</groupId>
    <artifactId>macula-boot-starter-prometheus</artifactId>
    <version>${macula.version}</version>
</dependency>
```



## 使用说明

### AuditLog

添加macula-boot-starter-auditlog依赖后，在需要审计的方法上加上@AuditLog注解，添加注解后调用方法会触发OperLogEvent事件，可以定义Listener监听事件用于持久化审计日志。

```java
  @Operation(summary = "新增菜单")
  @AuditLog(title = "新增菜单")
  @PostMapping
  @CacheEvict(cacheNames = "system", key = "'routes'")
  public boolean addMenu(@RequestBody SysMenu menu) {
      boolean result = menuService.saveMenu(menu);
      return result;
  }
```

@AuditLog注解定义如下：

```java
@Target({ElementType.PARAMETER, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface AuditLog {
    /**
     * 模块
     */
    String title() default "";

    /**
     * 功能
     */
    BusinessType businessType() default BusinessType.OTHER;

    /**
     * 操作人类别
     */
    OperatorType operatorType() default OperatorType.MANAGE;

    /**
     * 是否保存请求的参数
     */
    boolean isSaveRequestData() default true;

    /**
     * 是否保存响应的参数
     */
    boolean isSaveResponseData() default true;

    /**
     * 排除指定的请求参数
     */
    String[] excludeParamNames() default {};

}
```

Listenter定义参考：

```java
@Component
@RequiredArgsConstructor
public class AuditLogEventListener {

    private final SysLogService sysLogService;

    /**
     * 保存系统日志记录
     */
    @Async
    @EventListener
    public void saveLog(OperLogEvent operLogEvent) {
        SysLog log = new SysLog();
        log.setOpIp(operLogEvent.getOperIp());
        log.setErrorMsg(operLogEvent.getErrorMsg());
        log.setJsonResult(operLogEvent.getJsonResult());
        log.setOpName(operLogEvent.getOperName());
        log.setOpParam(operLogEvent.getOperParam());
        log.setOpMethod(operLogEvent.getMethod());
        log.setOpStatus(operLogEvent.getStatus());
        log.setOpUrl(operLogEvent.getOperUrl());
        log.setOpRequestMethod(operLogEvent.getRequestMethod());
        log.setOpTitle(operLogEvent.getTitle());
        log.setCreateBy(operLogEvent.getOperName());
        if (operLogEvent.getOperTime() != null) {
            log.setCreateTime(LocalDateTime.ofInstant(operLogEvent.getOperTime().toInstant(), ZoneId.systemDefault()));
        } else {
            log.setCreateTime(LocalDateTime.now());
        }
        sysLogService.save(log);
    }
}
```



### Logstash

添加macula-boot-starter-logstash依赖，在你的logback的配置文件中，加入include，并且需要再application.yml中定义logstash.address配置logstash地址。

```xml
<include resource="logback-logstash.xml" />
```

上述inlcude的内容如下：

```xml
<included>
    <!-- logstash地址，从 application.yml 中获取-->
    <springProperty scope="context" name="LOGSTASH_ADDRESS" source="logstash.address"/>
    <springProperty scope="context" name="APPLICATION_NAME" source="spring.application.name"/>

    <!--输出到logstash的appender-->
    <appender name="logstash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <!--可以访问的logstash日志收集端口-->
        <destination>${LOGSTASH_ADDRESS}</destination>
        <encoder charset="UTF-8" class="net.logstash.logback.encoder.LogstashEncoder">
            <customFields>{"spring.application.name":"${APPLICATION_NAME}"}</customFields>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="logstash"/>
    </root>
</included>
```



### Skylog

添加macula-boot-starter-skylog依赖，在你的logback的配置文件中，加入include

```xml
<include resource="logback-skylog.xml" />
```

上述include的内容如下：

```xml
<included>
    <!-- 控制台输出 tid -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.TraceIdPatternLogbackLayout">
                <pattern>[%tid] ${console.log.pattern}</pattern>
            </layout>
            <charset>utf-8</charset>
        </encoder>
    </appender>

    <!-- skywalking 采集日志 -->
    <appender name="sky_log" class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.log.GRPCLogClientAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.TraceIdPatternLogbackLayout">
                <pattern>[%tid] ${console.log.pattern}</pattern>
            </layout>
            <charset>utf-8</charset>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="console"/>
        <appender-ref ref="sky_log"/>
    </root>
</included>
```



### Prometheus

引入macula-boot-starter-prometheus即可。其他在Prometheus控制台配置。



## 依赖引入

- logstash

  ```xml
  <dependencies>
      <dependency>
          <groupId>net.logstash.logback</groupId>
          <artifactId>logstash-logback-encoder</artifactId>
      </dependency>
  </dependencies>
  ```

  

## 版权说明

上述代码来源于[RuoYi](https://plus-doc.dromara.org)并适当修改。基于MIT协议：https://gitee.com/dromara/RuoYi-Cloud-Plus/blob/master/LICENSE
