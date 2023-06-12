---
title: "注释规约"
linkTitle: "注释规约"
weight: 2
---

## 概述

良好的注释可以帮助自己和其他开发者理解代码结构，也可以用在项目编译时生成javadoc，避免重复工作。

## 类注释

所有的类都必须使用Javadoc，添加创建者和创建日期及描述信息，不得使用 // xxx 方式。

       /**
        * <p>
        * description
        * </p>
        * 
        * @author xxxx@hand-china.com 2018/06/07 13:48
        */
        public class Demo {
       }
       
## 方法注释

所有的抽象类、接口中的方法必须要用 Javadoc 注释、除了返回值、参数、异常说明外，还必须指出该方法做什么事情，实现什么功能。对子类的实现要求，或者调用注意事项，请一并说明。

      /**
       * <p>description<p/>
       *
       * @param name meaning
       * @param list meaning
       * @return the return
       * @throws RuntimeException exception description
       */
      String test(String name, List<String> list) throws RuntimeException;
      
方法内部单行注释，在被注释语句上方另起一行，使用//注释。方法内部多行注释使用/* */注释，注意与代码对齐。

      public void test(){
          // 单行注释
          String single = "";
          /*
          * 多行注释
          * 多行注释
          */
          String multi = "";
      }
      
私有方法可以使用 // xxx 注释，也可以使用Javadoc注释

      // description
      private void test3 () {
          // ...
          /* ... */
      }
      
## 字段注释

实体属性使用Javadoc注释，标明字段含义，这样getter/setter会含有注释。

public 属性必须使用Javadoc注释

      /**
       * 静态字段描述
       */
      public static final String STATIC_FIELD = "DEMO";
      /**
       * 姓名
       */
      private String name;
      
## 特殊注释标记

待办事宜（TODO）:（ 标记人，标记时间，[预计处理时间]）表示需要实现，但目前还未实现的功能。

错误（FIXME）:（标记人，标记时间，[预计处理时间]）在注释中用 FIXME 标记某代码是错误的，而且不能工作，需要及时纠正的情况。

        public void test3 () {
            // TODO 待完成 [author, time]
            // FIXME 待修复 [author, time]
        }
        
## 分隔符

有时需要划分区块，可以使用分隔符进行分隔

      //
      // 说明
      // ------------------------------------------------------------------------------
      //===============================================================================
      //  说明
      //===============================================================================

## 其它

+ 同一个类多个人开发，如有必要请在类、方法上加上负责人、时间信息
+ 代码修改的同时，注释也要进行相应的修改，尤其是参数、返回值、异常、核心逻辑等的修改
+ 对于注释的要求：第一、能够准确反应设计思想和代码逻辑；第二、能够描述业务含义，使别的程序员能够迅速了解到代码背后的信息。
+ 好的命名、代码结构是自解释的，注释力求精简准确、表达到位。避免出现注释的一个极端：过多过滥的注释，代码的逻辑一旦修改，修改注释是相当大的负担。
+ 大段代码注释请注意格式化。

## 参考文章

[《汉得开放平台文档》](https://open.hand-china.com/document-center/doc/product/10067/10239?doc_id=34377&doc_code=6205#%E5%88%86%E9%9A%94%E7%AC%A6)
