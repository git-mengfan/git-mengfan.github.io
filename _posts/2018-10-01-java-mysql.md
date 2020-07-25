---
layout: post
title: Java连接MySQL数据库
categories: Blog
description: Java项目连接MySQL数据库
keywords: Java，MySQL
---
### 需求
新项目需要用java实现后台，需要用连接到数据库，作为学习过程记录了本文档。

### 环境需求
- macOS 10.15.6
- IntelliJ IDEA 2019.3
- Java 11.0.4
- Maven 3.6.3

### 操作步骤
- 在pom.xml文件中添加依赖：

```
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.21</version>
</dependency>
```

- 代码中引用，并开始编程：

```
package demo;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
public class Demo {
	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		Class.forName("com.mysql.cj.jdbc.Driver");
		Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/demo?useSSL=FALSE&serverTimezone=UTC","root", "123456");
		Statement cursor = conn.createStatement();
		ResultSet ret = cursor.executeQuery("select sum(count) as total from demo_db");
		while (ret.next()) {
			System.out.print(ret.getString("total") + "\t");
		}
	}
}
```

### 注意事项：
- 驱动为"com.mysql.cj.jdbc.Driver"，老版本中为“com.mysql.jdbc.Driver”
- 时区需要修改为本地时区
