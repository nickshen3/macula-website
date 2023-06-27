---
title: "应用开发流程"
linkTitle: "应用开发流程"
weight: 1
---

## 概述

后端应用的开发流程步骤如下：

+ 开发环境准备（如SDK、IDE）
+ 创建Maven项目，引入MaculaBoot中所需的依赖
+ 按经典的三层架构或DDD架构组织项目包并进行编码开发

下面以经典的三层架构为例，对开发步骤进行说明。开发环境准备与maven项目创建部分不再赘述。另外DDD架构示例，可以参考MaculaBoot源码中提供的demo。

## 应用开发步骤

### 数据表初始化

#### 数据表设计

![image](../images/dbt.png)

#### 生成创建表sql

        -- `macula-base`.sys_kms_tenant definition
        
        CREATE TABLE `sys_kms_tenant` (
          `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '表id,自增主键',
          `app_id` varchar(100) NOT NULL COMMENT '应用id，密钥所属应用',
          `key_name` varchar(30) NOT NULL COMMENT '密钥名称',
          `public_key` longtext NOT NULL COMMENT '密钥公钥',
          `private_key` longtext COMMENT '密钥私钥',
          `create_by` varchar(50) NOT NULL COMMENT '创建人， 自动填充字段',
          `create_time` datetime NOT NULL COMMENT '创建时间， 自动填充字段',
          `last_update_by` varchar(50) NOT NULL COMMENT '最后修改人， 自动填充字段',
          `last_update_time` datetime NOT NULL COMMENT '最后修改时间， 自动填充字段',
          `tenant_id` bigint(20) NOT NULL COMMENT '所属租户数据，租户隔离',
          PRIMARY KEY (`id`),
        ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8 COMMENT='密钥表';
        
        ALTER TABLE `sys_kms_tenant` ADD INDEX index_key_name ( `key_name` )

### 编写pojo-实体对象层

#### 说明

pojo主要包含单表对应的entity类及多表关联的bo类，主要用于在Dao(持久)层返回对象的包装。

#### 编写entity

entity常用注解：

        @TableName("sys_kms_tenant") // 类注解、标识该entity对应的数据库表为sys_kms_tenant
        @TableLogic(value = "0", delval = "1") //字段注解、标识作为逻辑删除字段，当使用框架Dao方法查询时默认过滤该字段为1的数据
        @TableField(exist = false) //字段注解、标识字段对应的字段名、填充支持及不检查字段存在等等
        @TableId( value = "id", type = IdType.AUTO ) // 字段注解、标识字段作为实体主键

entity编写：

        // 基础实体类基类
        @Getter
        @Setter
        public class BaseEntity implements Serializable {
        
            private static final long serialVersionUID = 1L;
        
            @JsonSerialize(using = ToStringSerializer.class)
            @Schema(description = "主键id")
            @TableId(value = "id", type = IdType.AUTO)
            private Long id;
        
            @Schema(description = "创建人")
            @TableField(fill = FieldFill.INSERT)
            private String createBy;
        
            @Schema(description = "更新人")
            @TableField(fill = FieldFill.INSERT)
            private LocalDateTime createTime;
        
            @Schema(description = "创建时间")
            @TableField(fill = FieldFill.INSERT_UPDATE)
            private String lastUpdateBy;
        
            @Schema(description = "更新时间")
            @TableField(fill = FieldFill.INSERT_UPDATE)
            private LocalDateTime lastUpdateTime;
        
        }
        
        @TableName("sys_kms_tenant")
        @Data
        public class SysKms extends BaseEntity {
            @Schema(
                description = "应用id，密钥所属应用"
            )
            private Long appId;
            
            @Schema(
                description = "密钥名称"
            )
            private String keyName;
        
            @Schema(
                description = "密钥公钥"
            )
            private String publicKey;
        
            @Schema(
                description = "密钥私钥"
            )
            private String privateKey;
           
        }

bo编写：

        @Schema(description = "密钥应用多表查询对象")
        @Data
        public class KmsBo implements Serializable{
        
            @Schema(description = "ID")
            private Long id;
        
            @Schema(description = "应用对应ID")
            private Long appId;
        
            @Schema(description = "应用编码")
            private String appCode;
        
            @Schema(description = "密钥名称")
            private String keyName;
        
        }

### 编写Dao-持久层

#### 说明

通过使用Mapper接口类，完成与数据库的交互操作，包括但不限与查找、更新、删除、新增

#### Mapper编写

        @Mapper
        public interface SysKmsMapper extends BaseMapper<SysKms> {
            Page<KmsBo> listKmsPages(Page<SysKms> page, KmsPageQuery queryParams);
        }

#### 自定义sql编写-xml配置文件

在resources下创建文件mapper/SysKmsMapper.xml，编写内容：

        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
        <mapper namespace="dev.macula.cloud.system.mapper.SysKmsMapper">
        
            <select id="listKmsPages" resultType="dev.macula.cloud.system.pojo.bo.KmsBo">
                SELECT
                id,
                app_id,
                app_code,
                key_name,
                FROM
                sys_kms_tenant kms
                LEFT JOIN 
                sys_application_tenant app
                ON kms.app_id = app.id
                <where>
                    <if test='queryParams.keywords !=null  and queryParams.keywords.trim() neq ""'>
                        (kms.key_name like concat('%',#{queryParams.keywords},'%'))
                    </if>
                </where>
            </select>
        </mapper>

### 编写service-应用层

#### 说明

进行相关业务处理，对持久层数据做展示处理与展现层数据做持久处理等

#### service应用层接口编写

        public interface SysKmsService {
            Page<KmsVo> listKmsPages(KmsPageQuery queryParams);
        }

#### serviceImpl应用层实现编写

        @Service
        @RequiredArgsConstructor
        public class SysKmsServiceImpl extends ServiceImpl<SysKmsMapper, SysKms> implements SysKmsService {
            private final KmsConverter kmsConverter;
            @Override
            public Page<KmsVo> listKmsPages(KmsPageQuery queryParams) {
                Page<SysKms> page = new Page<>(queryParams.getPageNum(), queryParams.getPageSize());
                Page<KmsBo> bo = this.baseMapper.listKmsPages(page, queryParams);
                Page<KmsVo> list = kmsConverter.bo2Vo(bo);     //bo对象转成vo对象
                return list;
            }
        }

### 编写api-展现层

#### 说明

将传递进来的数据进行持久化以及将处理好的数据显示回去。传递进入的数据结构按业务分为1、Form对象（表单对象处理新增、修改）2、Query对象（查询对象处理查询入参）。显示对象包含基础响应对象加显示层返回对象，显示层返回对象主要分基础数据类型及Vo对象

#### Form对象编写

        @Schema(description = "密钥表单对象")
        @Data
        public class KmsForm {
            @Schema(description = "ID")
            private Long id;
            @Schema(description = "应用ID")
            private Long appId;
            @Schema(description = "密钥名称")
            private String keyName;
        }

#### Query对象编写

        // 基础分页query对象
        @Data
        @Schema
        public class BasePageQuery {
        
            @Schema(description = "页码", example = "1")
            private int pageNum = 1;
        
            @Schema(description = "每页记录数", example = "10")
            private int pageSize = 10;
        }
        
        @Data
        public class KmsPageQuery extends BasePageQuery {
            @Schema(description = "关键字(密钥名称)")
            private String keywords;
        }

#### 基础响应对象结构

        @Data
        public class Result<T> implements Serializable {
        
            private boolean success;
        
            private String code;
        
            private String msg;
        
            private T data;
        
            public static <T> Result<T> success() {
                return success(null);
            }
        
            public static <T> Result<T> success(T data) {
                return success(data, ApiResultCode.SUCCESS.getMsg());
            }
        
            public static <T> Result<T> success(T data, String msg) {
                Result<T> result = new Result<>();
                result.setSuccess(true);
                result.setCode(ApiResultCode.SUCCESS.getCode());
                result.setMsg(msg);
                result.setData(data);
                return result;
            }
        
            public static <T> Result<T> failed() {
                return failed(ApiResultCode.FAILED);
            }
        
            public static <T> Result<T> failed(ResultCode resultCode) {
                return failed(resultCode, null);
            }
        
            public static <T> Result<T> failed(ResultCode resultCode, T data) {
                // data是错误原因
                return failed(resultCode.getCode(), resultCode.getMsg(), data);
            }
        
            public static <T> Result<T> failed(String code, String msg, T data) {
                // data是错误原因
                Result<T> result = new Result<>();
                result.setSuccess(false);
                result.setCode(code);
                result.setMsg(msg);
                result.setData(data);
                return result;
            }
        
            public static <T> Result<T> judge(boolean status) {
                if (status) {
                    return success();
                } else {
                    return failed();
                }
            }
        }

#### vo对象编写

        @Schema(description = "密钥视图对象")
        @Data
        public class KmsVo implements Serializable{
        
            @Schema(description = "ID")
            private Long id;
        
            @Schema(description = "应用对应ID")
            private Long appId;
        
            @Schema(description = "应用编码")
            private String appCode;
        
            @Schema(description = "密钥名称")
            private String keyName;
        
        }

#### 展示层编写

        @Tag(name = "密钥接口", description = "密钥接口")
        @RestController
        @RequestMapping("/api/v1/kms")
        @RequiredArgsConstructor
        public class SysKmsController {
            private final SysKmsService sysKmsService;
        
            @Operation(summary = "获取密钥列表分页")
            @GetMapping
            public IPage<KmsVo> listKms(KmsPageQuery queryParams) {
                IPage<KmsVo> list = sysKmsService.listKmsPages(queryParams);
                return list;
            }
        }
        
