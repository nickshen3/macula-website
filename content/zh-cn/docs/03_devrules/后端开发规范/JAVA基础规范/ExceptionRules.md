---
title: "异常规约"
linkTitle: "异常规约"
weight: 4
---

## 概述

在软件开发过程中，合理的记录异常能够有效地帮助我们定位到问题。反之，如果异常记录不合理，会导致BUG难以排查。

## 异常分类

JAVA中有三种一般类型的可抛类: **检查性异常(checked exceptions)、非检查性异常(unchecked Exceptions)和错误(errors)**。

+ Checked exceptions：必须通过方法进行声明。这些异常都继承自Exception类。一个Checked exception声明了一些预期可能发生的异常。

+ Unchecked exceptions：不需要声明的异常。大多继承自RuntimeException。例如NullPointerException，ArrayOutOfBoundsException。同时这样的异常不应该捕获，而应该打印出堆栈信息。

+ Errors：大多是一些运行环境的问题，这些问题可能会导致系统无法运行。例如OutOfMemoryError，StackOverflowError。

## 用户自定义异常

我们应该遵循如下的规范。

+ 当应用程序出现问题时，直接抛出自定义异常。

      throw new DaoObjectNotFoundException("Couldn't find dao with id " + id);
      
+ 将自定义异常中的原始异常包装并抛出。

      catch (NoSuchMethodException e) {
        throw new DaoObjectNotFoundException("Couldn't find dao with id " + id, e);
      }

+ 不要吞下catch的异常

      try {
          System.out.println("Never do that!");
      } catch (AnyException exception) {
          // Do nothing
      }

  这样的捕获毫无意义。我们应该使用一定的日志输出来定位到问题。
  
+ 方法上应该抛出具体的异常，而不是Exception。

      public void foo() throws Exception { //错误方式
      }
      public void foo() throws SQLException { //正确方式
      }
      
+ 要捕获异常的子类，而不是直接捕获Exception。

      catch (Exception e) { //错误方式
      }
      
+ 永远不要捕获Throwable类。

+ 不要只是抛出一个新的异常，而应该包含堆栈信息。

    错误的做法：

      try {
          // Do the logic
      } catch (BankAccountNotFoundException exception) {
          throw new BusinessException();
          // or
          throw new BusinessException("Some information: " + e.getMessage());
      }
      
    正确的做法：

      try {
          // Do the logic
      } catch (BankAccountNotFoundException exception) {
          throw new BusinessException(exception);
          // or
          throw new BusinessException("Some information: " ,exception);
      }
      
+ 要么记录异常要么抛出异常，但不要一起执行。

      catch (NoSuchMethodException e) {  
         //错误方式 
         LOGGER.error("Some information", e);
         throw e;
      }
      
+ 不要在finally中再抛出异常。

      try {
        someMethod();  //Throws exceptionOne
      } finally {
        cleanUp();    //如果finally还抛出异常，那么exceptionOne将永远丢失
      }
      
  如果someMethod 和 cleanUp 都抛出异常，那么程序只会把第二个异常抛出来，原来的第一个异常（正确的原因）将永远丢失。
  
+ 始终只捕获实际可处理的异常。

      catch (NoSuchMethodException e) {
         throw e; //避免这种情况，因为它没有任何帮助
      }
      
  不要为了捕捉异常而捕捉，只有在想要处理异常时才捕捉异常。
  
+ 不要使用printStackTrace()语句或类似的方法。

+ 如果你不打算处理异常，请使用finally块而不是catch块。

+ 应该尽快抛出(throw)异常，并尽可能晚地捕获(catch)它。你应该做两件事：分装你的异常在最外层进行捕获，并且处理异常。

+ 在捕获异常之后，需要通过finally 进行收尾。在使用io或者数据库连接等，最终需要去关闭并释放它。

+ 不要使用if-else 来控制异常的捕获。

+ 一个异常只能包含在一个日志中。

      // 错误
      LOGGER.debug("Using cache sector A");
      LOGGER.debug("Using retry sector B");
      // 正确
      LOGGER.debug("Using cache sector A, using retry sector B");
      
+ 将所有相关信息尽可能地传递给异常。有用且信息丰富的异常消息和堆栈跟踪也非常重要。

+ 在JavaDoc中记录应用程序中的所有异常。应该用javadoc来记录为什么定义这样一个异常。

+ 异常应该有具体的层次结构。如果异常没有层次的话，则很难管理系统中异常的依赖关系。

  类似这样：
  
      class Exception {}
      class BusinessException extends Exception {}
      class AccountingException extends BusinessException {}
      class BillingCodeNotFoundException extends AccountingException {}
      class HumanResourcesException extends BusinessException {}
      class EmployeeNotFoundException extends HumanResourcesException {}
      
## 参考文章

 [《汉得开放平台文档》](https://open.hand-china.com/document-center/doc/product/10067/10239?doc_id=34378&doc_code=6208)
