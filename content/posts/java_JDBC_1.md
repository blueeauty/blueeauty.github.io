---
title: "Java之JDBC"
date: 2022-11-11T13:00:36+08:00
draft: false
---

##  JDBC

<!--more-->

什么是JDBC？JDBC是Java DataBase Connectivity的缩写，它是Java程序访问数据库的标准接口。

使用Java程序访问数据库时，Java代码并不是直接通过TCP连接去访问数据库，而是通过JDBC接口来访问，而JDBC接口则通过JDBC驱动来实现真正对数据库的访问。

例如，我们在Java代码中如果要访问MySQL，那么必须编写代码操作JDBC接口。注意到JDBC接口是Java标准库自带的，所以可以直接编译。而具体的JDBC驱动是由数据库厂商提供的，例如，MySQL的JDBC驱动由Oracle提供。因此，访问某个具体的数据库，我们只需要引入该厂商提供的JDBC驱动，就可以通过JDBC接口来访问，这样保证了Java程序编写的是一套数据库访问代码，却可以访问各种不同的数据库，因为他们都提供了标准的JDBC驱动：

```ascii
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐

│  ┌───────────────┐  │
   │   Java App    │
│  └───────────────┘  │
           │
│          ▼          │
   ┌───────────────┐
│  │JDBC Interface │◀─┼─── JDK
   └───────────────┘
│          │          │
           ▼
│  ┌───────────────┐  │
   │  JDBC Driver  │◀───── Vendor
│  └───────────────┘  │
           │
└ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ┘
           ▼
   ┌───────────────┐
   │   Database    │
   └───────────────┘
```

实际上，一个MySQL的JDBC的驱动就是一个jar包，它本身也是纯Java编写的。我们自己编写的代码只需要引用Java标准库提供的java.sql包下面的相关接口，由此再间接地通过MySQL驱动的jar包通过网络访问MySQL服务器，所有复杂的网络通讯都被封装到JDBC驱动中，因此，Java程序本身只需要引入一个MySQL驱动的jar包就可以正常访问MySQL服务器：

```ascii
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
   ┌───────────────┐
│  │   App.class   │  │
   └───────────────┘
│          │          │
           ▼
│  ┌───────────────┐  │
   │  java.sql.*   │
│  └───────────────┘  │
           │
│          ▼          │
   ┌───────────────┐     TCP    ┌───────────────┐
│  │ mysql-xxx.jar │──┼────────▶│     MySQL     │
   └───────────────┘            └───────────────┘
└ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
          JVM
```



### 快速入门

1. 创建工程，导入驱动jar包
2. 注册驱动 Class.forName("com.mysql.jdbc.Driver");
3. 获取连接 Connection conn=DirverManager.getConnection(url,username,password);
4. 定义SQL语句 String sql="update...";
5. 获取执行SQL语句 Statement stmt=conn.createStatement();
6. 执行SQL stmt.executeUpdate(sql);
7. 处理返回结果
8. 释放资源



```java
package com.itheima.jdbc;


import com.mysql.jdbc.Driver;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class JDBCDemo {
    public static void main(String[] args) throws Exception {
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
	    //mysql5之后，上面代码可以不写
        //2.获取连接
        String url="jdbc:mysql://127.0.0.1:3306/chip";
        String username="root";
        String password="root";
        Connection conn=DriverManager.getConnection(url,username,password);

        //3.定义sql
        String sql="update operator set chip_num=1000 where operator_index=3";

        //4.获取执行sql的对象Statement
        Statement stmt=conn.createStatement();

        //5.执行sql
        int count=stmt.executeUpdate(sql);//受影响的行数

        //处理结果
        System.out.println(count);

        //7.释放资源
        stmt.close();
        conn.close();
    }
}
```



## JDBC API详解

### DriverManager

DriverManager(驱动管理类)

作用:

1、注册驱动

```java
Class.forName("com.mysql.jdbc.Driver");
```

- 查看Driver源码

  ![](/img/13.png)

  

  提示

  - MYSQL5之后的驱动包，可以省略注册驱动的步骤
  - 自动加载jar包中META-INF/services/java.sql.Driver文件中的驱动类

2、获取数据库的连接对象

```java
static Connection				getConnection(String url,String user,String password)
```

- 参数

  1. url:	连接路径

  ```
  语法：jdbc:mysql://ip地址(域名):端口号/数据库名称?参数键值对1&参数键值对2...
  示例:	jdbc:mysql://127.0.0.1:3306/db1
  细节:
  如果连接的是本机mysql服务器，并且mysql服务器默认端口是3306，则url可以简写为: jdbc:mysql:///数据库名称?参数键值对
  配置useSSL=false参数，禁用安全连接方式，解决警告提示
  ```

  2. user:	用户名

  3. password:    密码

     



### Connection

**1.获取执行SQL对象**

- 普通执行SQL对象

  ```
  Statement createStatement()
  ```

- 预编译SQL的执行SQL对象:防止SQL注入

  ```
  PreparedStatement prepareStatement(sql)
  ```

- 执行存储过程的对象

  ```
  CallableStatement prepareCall(sql)
  ```

**2.事务管理**

- MySQL事务管理

  ```
  开启事务: BEGIN：/START TRANSACTION;
  提交事务: COMMIT;
  回滚事务: ROLLBACK
  
  MySQL默认自动提交事务
  ```

- JDBC事务管理: Connection接口中定义了3个对应的方法

  ```
  开启事务: setAutoCommit(boolean autoCommit); true为自动提交事务，false为手动提交事务，即为开启事务
  提交事务: commit()
  回滚事务: rollback()
  ```

  

### Statement

Statement作用:

**1、执行SQL语句**

- 执行SQL语句

  ```
  int executeUpdate(sql);	执行DML、DDL语句
  =》	返回值：(1)DML语句影响的行数 (2)DDL语句执行后，执行成功也可能返回0
  
  ResultSet executeQuery(sql): 执行DQL语句
  => 	返回值： ResultSet结果集对象
  ```

  

### ResultSet

- ResultSet(结果集对象)作用：

  1. ​	封装了DQL查询语句的结果

     ```
     ResultSet	stmt.executeQuery(sql):	执行DQL语句，返回ResultSet对象
     ```

- 获取查询结果

  ```
  boolean next():	(1)将光标从当前位置向前移动一行	(2)判断当前行是否为有效行
  =》	返回值
  	true:	有效行，当前行有数据
  	false:	无效行，当前行没有数据
  	
  xxx	getXxx(参数):	获取数据
  =》	xxx:	数据类型；如:	int getInt(参数);String getString(参数)
  =>	参数:
  	int:	列的编号,从1开始
  	String:	列的名称
  ```

- 使用步骤:

  1.游标向下移动一行，并判断该行是否有数据：next()

  2.获取数据:	getXxx(参数)

  ```
  //循环判断游标是否最后一行末尾
  while(rs.next()){
  	//获取数据
  	rs.getXxx(参数);
  }
  ```



```
package com.itheima.jdbc;


import org.junit.Test;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

/**
 * JDBC API 详解: ResultSet
 */

public class JDBCDemo5_ResultSet {
    /**
     * 执行DQL查询语句
     * @throws Exception
     */

    @Test
    public void testResultRet() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");

        //2.获取连接
        String url="jdbc:mysql:///chip?useSSL=false";
        String username="root";
        String password="root";
        Connection conn=DriverManager.getConnection(url,username,password);

        //3.定义sql
        String sql="select * from operator";

        //4.获取statement对象
        Statement stmt=conn.createStatement();

        //5.执行sql
        ResultSet rs=stmt.executeQuery(sql);

        //6.处理结果，遍历rs中的所有数据
        //6.1 光标向下移动一行，并且判断当前行是否有数据
        while (rs.next()){
            //6.2 获取数据
//            int id=rs.getInt(1);
//            String name=rs.getString(2);
//            int num=rs.getInt(3);

            int id=rs.getInt("operator_index");
            String name=rs.getString("student_name");
            int num=rs.getInt("chip_num");
            System.out.println(id);
            System.out.println(name);
            System.out.println(num);
            System.out.println("------------");
        }

        //7.释放资源
        rs.close();
        stmt.close();
        conn.close();
    }

}
```



### PreparedStatement

- PreparedStatement作用

  1.预编译SQL并执行SQL语句

  一、获取PreparedStatement对象

  ```
  //SQL语句中的参数值，使用?占位符替代
  String sql="select * from user where username =? and password = ?";
  
  //通过Connection对象获取，并传入对应的sql语句
  PreparedStatement pstmt=conn.prepareStatement(sql);
  ```

  二、设置参数值

  ```
  PreparedStatement对象:	setXxx(参数1，参数2):给？赋值
  =》	Xxx:	数据类型；	如setInt(参数1,参数2)
  =》	参数:
  	参数1：?的位置编号，从1开始
  	参数2: ?的值
  ```

  三、执行SQL

  ```
  executeUpdate();/executeQuery();	:不需要再传递sql
  ```

  

PreparedStatement 好处：

* 预编译SQL，性能更高
* 防止SQL注入：==将敏感字符进行转义==

![](/img/14.png)

Java代码操作数据库流程如图所示：

* 将sql语句发送到MySQL服务器端

* MySQL服务端会对sql语句进行如下操作

  * 检查SQL语句

    检查SQL语句的语法是否正确。

  * 编译SQL语句。将SQL语句编译成可执行的函数。

    检查SQL和编译SQL花费的时间比执行SQL的时间还要长。如果我们只是重新设置参数，那么检查SQL语句和编译SQL语句将不需要重复执行。这样就提高了性能。

  * 执行SQL语句

接下来我们通过查询日志来看一下原理。

* 开启预编译功能

  在代码中编写url时需要加上以下参数。而我们之前根本就没有开启预编译功能，只是解决了SQL注入漏洞。

  ```sql
  useServerPrepStmts=true
  ```

* 配置MySQL执行日志（重启mysql服务后生效）

  在mysql配置文件（my.ini）中添加如下配置

  ```
  log-output=FILE
  general-log=1
  general_log_file="D:\mysql.log"
  slow-query-log=1
  slow_query_log_file="D:\mysql_slow.log"
  long_query_time=2
  ```

* java测试代码如下：

  ```java
   /**
     * PreparedStatement原理
     * @throws Exception
     */
  @Test
  public void testPreparedStatement2() throws  Exception {
  
      //2. 获取连接：如果连接的是本机mysql并且端口是默认的 3306 可以简化书写
      // useServerPrepStmts=true 参数开启预编译功能
      String url = "jdbc:mysql:///db1?useSSL=false&useServerPrepStmts=true";
      String username = "root";
      String password = "1234";
      Connection conn = DriverManager.getConnection(url, username, password);
  
      // 接收用户输入 用户名和密码
      String name = "zhangsan";
      String pwd = "' or '1' = '1";
  
      // 定义sql
      String sql = "select * from tb_user where username = ? and password = ?";
  
      // 获取pstmt对象
      PreparedStatement pstmt = conn.prepareStatement(sql);
  
      Thread.sleep(10000);
      // 设置？的值
      pstmt.setString(1,name);
      pstmt.setString(2,pwd);
      ResultSet rs = null;
      // 执行sql
      rs = pstmt.executeQuery();
  
      // 设置？的值
      pstmt.setString(1,"aaa");
      pstmt.setString(2,"bbb");
      // 执行sql
      rs = pstmt.executeQuery();
  
      // 判断登录是否成功
      if(rs.next()){
          System.out.println("登录成功~");
      }else{
          System.out.println("登录失败~");
      }
  
      //7. 释放资源
      rs.close();
      pstmt.close();
      conn.close();
  }
  ```

  

### 数据库连接池

>* 数据库连接池是个容器，负责分配、管理数据库连接(Connection)
>
>* 它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个；
>
>* 释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏
>* 好处
>  * 资源重用
>  * 提升系统响应速度
>  * 避免数据库连接遗漏

