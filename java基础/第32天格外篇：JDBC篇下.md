# JDBC和连接池

1. JDBC概述
2. JDBC API\*
3. **JDBC Utils**
4. **事务\*，批处理，连接池\***
5. **工具 Apache公司的 DBUtils**
6. **BasicDao（DAO增删改查）**



## 3. **JDBC Utils**

### 说明

在JDBC操作中，经常需要使用到 获取连接 和 释放资源(断开连接) 等操作。

可以将这类操作封装成一个工具类 JDBCUtils（称jdbc工具类）。

### 具体代码

```java
public class JdbcUtil {
    //因为只需要设置一次，做成静态属性
    private static String user;
    private static String password;
    private static String url;
    private static String driver;
	//类静态初始化信息，在类加载时候就初始化。
    static {
        try {
            Properties properties = new Properties();
            properties.load(new FileReader("src//mysql.properties"));
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            driver = properties.getProperty("driver");
            url = properties.getProperty("url");

        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }
    
	//1.得到连接
    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }
	//2.关闭资源
    public static void close(ResultSet resultSet, Statement statement, Connection connection) {
        try {
            if (resultSet != null) {
                resultSet.close();
            }
            if (statement != null) {
                statement.close();
            }
            if(connection!=null){
                connection.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }
}
```



## 4.1事务

即Mysql学过的内容。

### 介绍

![image-20220413161524958](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220413161524958.png)



### 具体操作

通过Connection来操作调用事务。

- connection.setAutoCommit(false); 即默认开启事务
- connection.commit(); 提交事务。
- connection.rollback(); 填保存点，默认回滚到最启动事务时。

```java
public void transaction() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;
    //获取连接
    connection = JdbcUtil.getConnection();
    String sql1 = "update emp set salary = salary-3000 where id = 1";
    String sql2 = "update emp set salary = salary+3000 where id = 12";;
    try {
        //设置取消自动提交事务，即开启事务。
        connection.setAutoCommit(false);
        //SQL操作1
        preparedStatement = connection.prepareStatement(sql1);
        preparedStatement.executeUpdate();
        //int i = 1/0;
        //SQL操作2
        preparedStatement = connection.prepareStatement(sql2);
        preparedStatement.executeUpdate();
        //正常运行，提交事务
        connection.commit();

    } catch (SQLException e) {
        e.printStackTrace();
        try {
            //若发生异常，则回滚。不填保存点，默认回滚到最开始启动事务时候（即 connection.setAutoCommit(false)）
            connection.rollback();
            System.out.println("发生异常，数据回滚");
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }finally {
        //关闭连接
        JdbcUtil.close(resultSet, preparedStatement, connection);
    }

}
```



## 4.2批处理

### 介绍

![image-20220413171641518](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220413171641518.png)

减少网络传输，一次性发送。

### 具体操作

- preparedStatement.addBatch()	 加入
- preparedStatement.executeBatch()  执行
- preparedStatement.clearBatch()   清空

```java
  public void batch() throws SQLException {
	
        String sql = "insert into admin values(null,?,?)";
        Connection connection = JdbcUtil.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
      
        for (int i = 0; i < 2000; i++) {
            preparedStatement.setString(1,"a"+i);
            preparedStatement.setString(2,"123");
            //把该preparedStatement加入batch中
            preparedStatement.addBatch();
            if((i%1000)==0){
             //把batch中所有该preparedStatement批量执行
                preparedStatement.executeBatch();
             //把batch中的数据清空，准备装下一波新的preparedStatement 
                preparedStatement.clearBatch();
            }
        }
      
        JdbcUtil.close(null,preparedStatement,connection);
    }

}
```



## 4.3 数据库连接池

### 前言

传统获取Connection的问题：

![image-20220413202907979](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220413202907979.png)

### 介绍

![image-20220413203550799](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220413203550799.png)

数据库连接池中的close并不是真的关闭连接，只是把连接放回到连接池中，等待复用。

![image-20220414190406148](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220414190406148.png)

### 数据库种类

![image-20220414190945732](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220414190945732.png)

因为DataSource是接口，需要导入第三方公司的实现类。如：使用C3P0连接池，就需要导入该公司提供的jar包。

### C3P0

配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<c3p0-config>
    <!--    数据源名称，代表连接池-->
    <named-config name="mySource">
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/db01</property>
        <property name="user">root</property>
        <property name="password">cong.0312</property>

        <property name="initialPoolSize">10</property>
        <property name="maxIdleTime">30</property>
        <property name="maxPoolSize">100</property>
        <property name="minPoolSize">10</property>
        <property name="maxStatements">5</property>
        <property name="maxStatementsPerConnection">2</property>
    </named-config>
</c3p0-config>
```

具体操作：

```java
public void c3p0() throws SQLException {

    ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("mySource");
    Connection connection = comboPooledDataSource.getConnection();
    //System.out.println(connection);
    connection.close();
}
```

### druid德鲁伊

该数据库连接池技术，速度更快。

配置文件：

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/db01

username=root
password=cong.0312

initialSize=5
maxActive=10
maxWait=5000
validationQuery=SELECT 1
testWhileIdle=true
```

具体操作：

```java
public void druid() throws Exception {

    Properties properties = new Properties();
    properties.load(new FileReader("src//druid.properties"));

    DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
    Connection connection = dataSource.getConnection();
    System.out.println(connection);
    connection.close();

}
```



### druid工具类

```java
public class JdbcUtilByDruid {

    private static DataSource dataSource;
	//初始化
    static {
        Properties properties = new Properties();
        try {
            properties.load(new FileReader("src//druid.properties"));
            dataSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
	//连接
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }
	//关闭。此处close方法不是真的关闭连接，只是把连接返回数据库连接池
    public static void close(ResultSet resultSet, Statement statement, Connection connection){
        try {
            if(resultSet!=null){
                resultSet.close();
            }
            if(statement!=null){
                statement.close();
            }
            if(connection!=null){
                connection.close();
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```



## 5. DBUtils

### 前言



![image-20220414210225527](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220414210225527.png)

解决办法：

把数据库数据映射成java里的类，采用集合来存储（java存储resultSet）。

- 封装的java类也称：JavaBean，PoJo，Domain

引出DBUtil工具，该工具提供了一整套简洁的方法。



### 介绍

![image-20220414212315923](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220414212315923.png)



### 使用

![image-20220414212329156](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220414212329156.png)

查询具体使用：

```java
public void DBUtil() throws SQLException {

    Connection connection = JdbcUtilByDruid.getConnection();
    String sql = "select * from emp where salary >= ?";
	//创建一个QueryRunner类
    QueryRunner queryRunner = new QueryRunner();
    /*
    1.query方法是执行sql语句，得到resultset，然后再把得到resultset结果封装到 Arraylist集合中。
    2.返回集合
    参数分析：
    	1）connection连接
    	2）sql语句
    	3）把resultset取出，封装到Actor对象中，再加入到ArrayList集合中。（需要传class对象是因为，地产使用反射机制，去获取Actor类的属性，进行封装）
    	4）给sql语句的?赋值，可以多个值，采用的可变参数。
    */
    List<Emp> list = queryRunner.query(connection, sql, new BeanListHandler<>(Emp.class), 10000);
    for (Emp emp : list) {
        System.out.println(emp);
    }
    JdbcUtilByDruid.close(null, null, connection);
}
```

- 底层会自动关闭resultset，PreparedStatement。
- new XxxHandler<>(类名.class)



dml具体使用：

使用update方法；

![image-20220415141811591](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220415141811591.png)



## 6. BasicDao



### 前言

![image-20220415143430795](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220415143430795.png)

![image-20220415144109068](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220415144109068.png)



### 介绍

![image-20220415144431435](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220415144431435.png)



### 具体实现

![image-20220415144650048](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220415144650048.png)

BasicDao类：

```java
public class BasicDao<T> {

    private QueryRunner queryRunner = new QueryRunner();
	//dml操作
    public int update(String sql, Object... parameters) {
        Connection connection = null;
        try {
            connection = JdbcUtilByDruid.getConnection();
            int rows = queryRunner.update(connection, sql, parameters);
            return rows;
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }finally {
            JdbcUtilByDruid.close(null,null,connection);
        }

    }
	//查询操作
    public List<T> query(Class clazz,String sql, Object... parameters)  {
        Connection connection = null;
        try {
            connection = JdbcUtilByDruid.getConnection();
            return queryRunner.query(connection, sql, new BeanListHandler<T>(clazz), parameters);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }finally {
            JdbcUtilByDruid.close(null,null,connection);
        }
    }
}
```

XxxDao类：

例如AdminDao类，基础BasicDao类

```Java
public class AdminDao extends BasicDao<Admin>{

	//继承所有BasicDao的方法
	//在此编写自己的特有方法

}
```



### 实际开发

由上到下。

---

**AppView界面层**

{显示界面}

**Service业务层**（ActorService，GoodsService... 一张表一个Service）

{组织sql，并调用XxxDao。因为需要操作好几张表，调用好几个不同的Dao}

**XxxDao层** （继承于BasciDao）

{对数据的增删改查。可有特有操作}

 		**BasciDao层**

​		{通用的增删改查操作}

**domain层**

{对应数据库表的 类}

---

**Test测试层**

