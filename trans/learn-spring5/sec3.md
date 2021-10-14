## 第三部分：写日志方面。

* * *

1.  在 com.packt.ch04.aspects 包中创建 MyLoggingAspect 作为 Java 类，该包将具有 before Advice 的方法。
2.  在其中添加类型为 org.apache.log4j.Logger 的数据成员。
3.  在其中添加一个 beforeAdvise（）方法。方法的签名可以是任何东西，这里我们添加 JoinPoint 作为参数。使用此参数，我们可以获得有关应用方面的类的信息。代码如下：

```java
      public class MyLoggingAspect { 
        Logger logger=Logger.getLogger(getClass()); 
        public void beforeAdvise(JoinPoint joinPoint) { 
          logger.info("method will be invoked :- 
            "+joinPoint.getSignature());   
        }       
      } 

```

4.  现在必须分三步在 XML 中配置方面：

*为 AOP 添加命名空间：

```
      <beans      
        xmlns:aop="http://www.springframework.org/schema/aop"  
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd  
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd">
```

5.  现在，我们可以通过使用“AOP”命名空间来使用 AOP 的标记：

*为方面添加 bean。

6.  为我们希望在 connection_new.xml 的应用程序中使用的方面添加 bean，如下所示：

```
<bean id="myLogger"
  class="com.packt.ch04.aspects.MyLoggingAspect" />
```

*配置方面。

7.  每个<aspect>都允许我们在<config>标记中写入方面。</config></aspect>
8.  每个方面都将 id 和 ref 作为属性。“ref”是指将调用其方法以提供建议的 bean。
9.  为切入点表达式配置 advice，并为 advice 调用哪个方法。可使用<before>标签在<aspect>内部配置前通知。</aspect></before>
10.  让我们在建议应用“myLogger”方面之前编写代码，该方面将在 BookDAO 的 addBook（）方法之前调用。配置如下所示：

```
      <aop:config>
        <aop:aspect id="myLogger" ref="logging">
          <aop:pointcut id="pointcut1"
            expression="execution(com.packt.ch03.dao.BookDAO.addBook
            (com.packt.ch03.beans.Book))" />
          <aop:before pointcut-ref="pointcut1" 
            method="beforeAdvise"/>
        </aop:aspect>
      </aop:config>
```

11.  执行 MainBookDAO_operation.java 以在控制台上获得以下输出：

```
      0 [main] INFO       org.springframework.context.support.ClassPathXmlApplicationContext -       Refreshing       org.springframework.context.support.ClassPathXmlApplicationContext@5      33e64: startup date [Sun Oct 02 23:44:36 IST 2016]; root of       context hierarchy
      66 [main] INFO       org.springframework.beans.factory.xml.XmlBeanDefinitionReader -       Loading XML bean definitions from class path resource       [connection_new.xml]
      842 [main] INFO       org.springframework.jdbc.datasource.DriverManagerDataSource - Loaded       JDBC driver: com.mysql.jdbc.Driver
      931 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - method       will be invoked :-int com.packt.ch03.dao.BookDAO.addBook(Book)
      book inserted successfully
      book updated successfully
      book deleted successfully
```

这里 BookDAO_JdbTemplate 作为目标对象，其代理将在运行时通过编织 addBook（）和 beforeAdvise（）方法的代码创建。现在，一旦我们了解了这个过程，让我们通过以下步骤在应用程序中逐个添加不同的切入点和建议。

### 注

在同一个连接点上可以应用多个 advice，但是为了简单理解切入点和 advice，我们每次都会保留一个 advice 和已经编写的注释。

### 通知后添加。

让我们为 BookDAO 中的所有方法添加 after-advice。

1.  在 MyLoggingAspect 中为 after advise 添加方法 afterAdvise（），如下所示：

```
      public void afterAdvise(JoinPoint joinPoint) { 
       logger.info("executed successfully :- 
         "+joinPoint.getSignature()); 
      } 

```

2.  将切入点表达式配置为针对 BookDAO 类内的所有方法，并在“myLogger”特性内的 connection_new.xml 中设置 after advice，如下所示：

```
      <aop:pointcut id="pointcut2"   
        expression="execution(com.packt.ch03.dao.BookDAO.*(..))" /> 
      <aop:after pointcut-ref="pointcut2" method="afterAdvise"/>
```

3.  执行 MainBookDAO_operations.java 以获得以下输出：

```
999 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - method will be invoked :-int com.packt.ch03.dao.BookDAO.addBook(Book)
1360 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - executed successfully :-int com.packt.ch03.dao.BookDAO.addBook(Book)
book inserted successfully
1418 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - executed successfully :-int com.packt.ch03.dao.BookDAO.updateBook(long,int)
book updated successfully
1466 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - executed successfully :-boolean com.packt.ch03.dao.BookDAO.deleteBook(long)
book deleted successfully
```

带下划线的语句清楚地表明，在所有方法之后调用了 advice。

### 返回通知后添加。

虽然我们编写了 after advice，但是我们无法获得从业务逻辑方法返回的值。返回后将通过以下步骤帮助我们获取返回值。

1.  在 MyLoggingAspect 中添加一个方法 returnAdvise（），该方法将在返回后被调用。代码如下所示：

```
      public void returnAdvise(JoinPoint joinPoint, Object val) { 
        logger.info(joinPoint.getSignature()+ " returning val" + val); 
      } 

```

参数“val”将保存返回的值。

2.  在“myLogger”下配置通知。我们不必配置切入点，因为我们将重用 alredy 配置的。如果要使用不同的连接点集，首先需要配置不同的切入点表达式。我们的配置如下所示：

```
      <aop:after-returning pointcut-ref="pointcut2"
        returning="val" method="returnAdvise" />
```

哪里

*returning-表示用于指定返回值将传递到的参数名称的属性。在我们的例子中，这个名称是“val”，它绑定在通知参数中。

3.  为了使输出易于理解，请在 advise 配置之前和之后添加注释，然后执行 MainBookDAO_operations.java 以获得控制台输出上的以下行：

```
      1378 [main] INFO  com.packt.ch04.aspects.MyLoggingAspect  - int       com.packt.ch03.dao.BookDAO.addBook(Book)  
      returning val:-1 
      1426 [main] INFO  com.packt.ch04.aspects.MyLoggingAspect  - int       com.packt.ch03.dao.BookDAO.updateBook(long,int) returning val:-1 
      1475 [main] INFO  com.packt.ch04.aspects.MyLoggingAspect  -
      boolean com.packt.ch03.dao.BookDAO.deleteBook(long)
      returning val:-true 

```

每个语句都显示从连接点返回的值。

### 增加周边建议。

正如我们已经讨论过的，只有在执行成功的情况下，才会在业务逻辑方法之前和之后调用 advice。让我们在应用程序中添加以下内容：

1.  在 MyLoggingAspect 中添加一个 aroundAdvise（）方法。此方法必须有一个参数作为 ProceedingJoinPoint，以便于应用程序流向连接点。代码如下：

![](https://www.packtpub.com/graphics/9781787120341/graphics/image_04_003.png)

继续之前的部分（）将在 B.L.方法之前调用，我们称之为“预处理”。ProceedJoinPoint 的 procedure（）方法将流带到相应的连接点。如果连接点成功执行，则继续后的部分将被执行，我们称之为“后处理”。在这里，我们通过计算预处理和后处理的时间差来计算完成该过程所需的时间。

我们想要编织方面的连接点返回 int，因此 aroundAdvise（）方法也返回相同类型的值。如果我们添加 void 而不是 int，我们将得到以下异常：

```
      Exception in thread "main" 
      org.springframework.aop.AopInvocationException: Null return value       from advice does not match primitive return type for: public   
      abstract int  
      com.packt.ch03.dao.BookDAO.addBook(com.packt.ch03.beans.Book) 

```

2.  现在，让我们在“myLogger”中添加一些建议，如下所示，

```
      <aop:around pointcut-ref="pointcut1" method="aroundAdvise" />
```

3.  在评论之前配置的建议时，在以下登录控制台上执行 MainBookDAO，

```
      1016 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - around       advise before int com.packt.ch03.dao.BookDAO.addBook(Book)  
      B.L.method getting invoked
      1402 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - number       of rows affected:-1
      1402 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - around
      advise after int com.packt.ch03.dao.BookDAO.addBook(Book)
      B.L.method getting invoked
      1403 [main] INFO com.packt.ch04.aspects.MyLoggingAspect - int
      com.packt.ch03.dao.BookDAO.addBook(Book) took 388 to complete
```

### 投料后添加

正如我们所知，一旦匹配的连接点抛出异常，就会触发 after-throwing 通知。在执行 JDBC 操作时，如果我们尝试在 book 表中添加重复条目，则会抛出 DuplicateKeyException。我们只想在抛出 Advice 后通过以下步骤进行记录，

1.  在 MyLoggingAspect 中添加 throwingAdvice（）方法，如下所示：

```
      public void throwingAdvise(JoinPoint joinPoint,  
        Exception exception) 
      { 
        logger.info(joinPoint.getTarget().getClass().getName()+"  
          got and exception" + "\t" + exception.toString()); 
      } 

```

开发人员可以自由选择签名，但是由于连接点方法将抛出异常，为 advice 编写的方法将有一个类型为 exception 的参数，以便我们可以记录它。我们还添加了 JoinPoint 类型的参数，因为我们想要处理方法签名

2.  在“myLogger”配置中的 connection_new.xml 中添加配置。要添加的配置为：

```
      <aop:after-throwing pointcut-ref="pointcut1"
        method="throwingAdvise" throwing="exception" />
```

<after- throwing="">将采取以下措施：</after->

***切入点参考**-我们用来编织接合点的切入点参考的名称

***方法**-抛出异常时将调用的方法的名称

***throwing**-要从异常将传递到的 advice 方法签名绑定的参数的名称。我们使用的方法签名中的参数名称为“exception”。

3.  执行 MainBookDAO_ 操作，有目的地添加其 ISBN 已存在于 book 表中的图书。在执行之前，请评论为其他建议添加的先前配置。我们将获得以下输出：

```
      1322 [main] ERROR com.packt.ch04.aspects.MyLoggingAspect  - int 
      com.packt.ch03.dao.BookDAO.addBook(Book) got and exception  
      org.springframework.dao.DuplicateKeyException: 
      PreparedStatementCallback; SQL [insert into book 
      values(?,?,?,?,?,?)]; Duplicate entry '9781235' for key 1; nested 
      exception is 
      com.mysql.jdbc.exceptions.jdbc4.MySQLIntegrityConstraintViolation
      Exception: Duplicate entry '9781235' for key 1 

```

4.  如果您添加了不同 ISBN 的图书，而图书表中还没有 ISBN，则不会显示上述错误日志，因为不会触发任何异常通知。

上面的演示清楚地说明了如何使用 XML 编写和配置方面。让我们继续编写基于注释的方面。