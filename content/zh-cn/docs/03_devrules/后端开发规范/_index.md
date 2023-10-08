---
title: "后端开发规范"
linkTitle: "后端开发规范"
weight: 3
skip_toc: true
skip_sidebar: true
no_list: true
content:
    - 编程规约:
        - name: 编程规约
          links:
            - '[命名风格](编程规约/命名风格)'
            - '[常量定义](编程规约/常量定义)'
            - '[代码格式](编程规约/代码格式)'
            - '[OOP规范](编程规约/oop规范)'
            - '[集合处理](编程规约/集合处理)'
            - '[并发处理](编程规约/并发处理)'
            - '[控制语句](编程规约/控制语句)'
            - '[注释规约](编程规约/注释规约)'
        - name: 异常日志
          links:
            - '[异常处理](异常日志/异常处理)'
            - '[日志规约](异常日志/日志规约)'
            - '[其他](异常日志/其他)'
        - name: 工程结构
          links:
            - '[应用分层](工程结构/应用分层)'
            - '[二方库依赖](工程结构/二方库依赖)'
            - '[服务器配置](工程结构/服务器)'
        - name: 其他
          links:
            - '[API规约](api规约)'
            - '[单元测试](单元测试)'
            - '[安全规约](安全规约)'
---

后端开发规范大部分来自阿里出品的[Java开发手册](https://github.com/alibaba/p3c)。建议使用IDE插件来检查下述规约。

{{% docs/document_box %}}

##### 附2：本手册专有名词
1. POJO（Plain Ordinary Java Object）: 在本手册中，POJO专指只有setter / getter / toString的简单类，包括DO/DTO/BO/VO等。 
2. GAV（GroupId、ArtifactId、Version）: Maven坐标，是用来唯一标识jar包。
3. OOP（Object Oriented Programming）: 本手册泛指类、对象的编程处理方式。 
4. ORM（Object Relation Mapping）: 对象关系映射，对象领域模型与底层数据之间的转换，本文泛指iBATIS, mybatis等框架。 
5. NPE（java.lang.NullPointerException）: 空指针异常。 
6. SOA（Service-Oriented Architecture）: 面向服务架构，它可以根据需求通过网络对松散耦合的粗粒度应用组件进行分布式部署、组合和使用，有利于提升组件可重用性，可维护性。 
7. 一方库: 本工程内部子项目模块依赖的库（jar包）。 
8. 二方库: 公司内部发布到中央仓库，可供公司内部其它应用依赖的库（jar包）。 
9. 三方库: 公司之外的开源库（jar包）。 
10. IDE（Integrated Development Environment）: 用于提供程序开发环境的应用程序，一般包括代码编辑器、编译器、调试器和图形用户界面等工具，本《手册》泛指IntelliJ IDEA和eclipse。 