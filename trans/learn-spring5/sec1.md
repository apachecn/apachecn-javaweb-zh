## 第一部分：为核心关注点（JDBC）创建应用程序

* * *

按照以下步骤创建基本应用程序：

1.  创建 Java 应用程序 CH04_JdbcTemplate_LoggingAspect，并向其中添加 Spring core、Spring JDBC、Spring aop、aspectjrt-1.5.3 和 aspectjweaver-1.5.3.jar 文件的 jar。
2.  在各自的软件包中复制所需的源代码文件和配置文件。该应用程序的最终概要如下所示：

![](https://www.packtpub.com/graphics/9781787120341/graphics/image_04_002.png)

3.  从应用程序的类路径中的 Ch03_JdbcTemplates 复制 connection_new.xml，并对其进行编辑以删除 id 为“namedTemplate”的 bean。