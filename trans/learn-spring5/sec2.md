## 第一部分：Log4J 集成

* * *

Log4j 是最简单的事情。让我们使用以下步骤进行集成：

1.  为了集成 Log4J，我们首先必须在应用程序中添加 Log4J-1.2.9.jar。
2.  使用以下配置在类路径中添加 log4j.xml 以添加控制台和文件附加器：

```java
      <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd"> 
      <log4j:configuration  
         xmlns:log4j='http://jakarta.apache.org/log4j/'> 
        <appender name="CA"  
          class="org.apache.log4j.ConsoleAppender"> 
          <layout class="org.apache.log4j.PatternLayout"> 
            <param name="ConversionPattern" value="%-4r [%t]  
              %-5p %c %x - %m%n" /> 
          </layout> 
        </appender> 
        <appender name="file"  
          class="org.apache.log4j.RollingFileAppender"> 
          <param name="File" value="C:\\log\\log.txt" /> 
          <param name="Append" value="true" /> 
          <param name="MaxFileSize" value="3000KB" /> 
          <layout class="org.apache.log4j.PatternLayout"> 
            <param name="ConversionPattern" value="%d{DATE}  
              %-5p %-15c{1}: %m%n" /> 
          </layout> 
        </appender> 
        <root> 
          <priority value="INFO" /> 
          <appender-ref ref="CA" /> 
          <appender-ref ref="file" /> 
        </root> 
      </log4j:configuration> 

```

您可以根据需要修改配置。

1.  现在，为了记录消息，我们将添加获取记录器的代码，然后添加记录机制。我们可以将代码添加到 BookDAO_JdbcTemplate.java 中，如下所示：

```
      public class BookDAO_JdbcTemplate implements BookDAO {  
        Logger logger=Logger.getLogger(BookDAO_JdbcTemplate.class); 
        public int addBook(Book book) { 
          // TODO Auto-generated method stub 
          int rows = 0; 
          String INSERT_BOOK = "insert into book  
            values(?,?,?,?,?,?)"; 
          logger.info("adding the book in table"); 

          rows=jdbcTemplate.update(INSERT_BOOK, book.getBookName(),  
            book.getISBN(), book.getPublication(),  
            book.getPrice(), 
            book.getDescription(), book.getAuthor()); 

            logger.info("book added in the table successfully"+  
              rows+"affected"); 
          return rows; 
        } 

```

不要担心，我们不会在每个类的应用程序中添加它，然后在方法中添加它，因为我们已经讨论了复杂性和重复性代码。让我们继续编写 aspect for logging 机制，通过以下步骤获得与上面编写的代码相同的结果。