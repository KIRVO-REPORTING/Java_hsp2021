## JDBC和连接池

1. **JDBC概述**
2. **JDBC API\***
3. JDBC Utils
4. 事务\*，批处理，连接池\*
5. 工具 Apache公司的 DBUtils
6. BasicDao（DAO增删改查）

 

### 1. JDBC概述

####  基本介绍

![image-20220411163422830](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411163422830.png)

不需要知道各种数据库的底层代码，让数据库厂商自己实现。

#### 原理图

![image-20220411163232982](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411163232982.png)

Java厂商制定一套规范，让各个数据库厂商实现该接口(实现了接口的jar包，称为驱动)，从而实现了java程序可以 调用不同的数据库。

#### 好处

![image-20220411164110526](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411164110526.png)



### 2. JDBC API

#### 概述

![image-20220411164232618](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411164232618.png)

#### 快速入门

1. 注册驱动
2. 得到连接
3. sql语句操作
4. 断开连接

第一步：

![image-20220411173940233](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411173940233.png)

第二步：

![image-20220411174031221](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411174031221.png)

第三步：

![image-20220411174123060](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411174123060.png)

第四步：

![image-20220411174135835](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411174135835.png)

```java
public class Jdbc01 {
    public static void main(String[] args) throws SQLException {
        //1.注册驱动
        Driver driver = new Driver();
        
        //2.得到连接
        String url = "jdbc:mysql://localhost:3306/db01?characterEncoding=utf8";
        Properties properties = new Properties();
        properties.setProperty("user","root");
        properties.setProperty("password","cong.0312");
        Connection connect = driver.connect(url,properties);
        
        //3.操作
        String sql = "update emp set ename = '欧阳' where id = 10;";
        //statement 用于执行静态sql语句，并会返回其生成结果的对象
        Statement statement = connect.createStatement();
        int rows = statement.executeUpdate(sql);
        System.out.println(rows >= 1 ? "成功插入":"插入失败");
        
        //4.断开连接
        statement.close();
        connect.close();
    }
}
```



#### 连接数据库的5种方式

```java
//第一种：静态加载
public void connect01() throws Exception {
        //1.注册驱动
        Driver driver = new Driver();
        //2.得到连接
        String url = "jdbc:mysql://localhost:3306/db01?characterEncoding=utf8";
        Properties properties = new Properties();
        properties.setProperty("user", "root");
        properties.setProperty("password", "cong.0312");
        Connection connect = driver.connect(url, properties);
    	//3.操作
    	System.out.println(connect);
        //4.断开连接
        connect.close();
    }

//第二种：使用反射机制，动态加载。更加灵活，减少依赖性。
public void connect02() throws Exception {
    	//1.注册驱动
        Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
        Driver driver = (Driver) aClass.newInstance();
        //2.得到连接
        String url = "jdbc:mysql://localhost:3306/db01?characterEncoding=utf8";
        Properties properties = new Properties();
        properties.setProperty("user", "root");
        properties.setProperty("password", "cong.0312");
        Connection connect = driver.connect(url, properties);
		//3.操作
        System.out.println(connect);
        //4.断开连接
        connect.close();
    }

//第三种：采用管理DriverManger统一管理驱动。无需dirver自己得到连接。
    public void connect03() throws Exception{
        //注册
        Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
        Driver driver = (Driver) aClass.newInstance();
        //连接
        String url = "jdbc:mysql://localhost:3306/db01?characterEncoding=utf8";
        String user = "root";
        String password = "cong.0312";
        DriverManager.registerDriver(driver); //注册驱动
        Connection connection = DriverManager.getConnection(url, user, password);
        //操作
        System.out.println(connection);
        //断开
        connection.close();
    }

/*
【推荐使用】
第四种：加载Driver类时候，会自动执行该类静态代码块。
	 static {
        try {
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
 即：1.创建了Driver实例 2.注册驱动
 所有该方法只需要：加载类，获取连接即可
*/
public void connect04() throws ClassNotFoundException, SQLException {
    //注册    
    	Class.forName("com.mysql.jdbc.Driver");
    //连接
        String url = "jdbc:mysql://localhost:3306/db01?characterEncoding=utf8";
        String user = "root";
        String password = "cong.0312";
        Connection connection = DriverManager.getConnection(url, user, password);
    //连接
        System.out.println(connection);
    //断开
        connection.close();
    }

```

新版jar包：

![image-20220411203005333](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411203005333.png)

service包下的内容：

![image-20220411203048813](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411203048813.png)

即JDBC4.0提供了驱动自动加载，DriverManager中的getConnection和getDrivers方法作了改进。在得到连接Connection前，就注册好了驱动。



**第五种方式：**

连接信息动态性，增加配置文件。让连接mysql更灵活。

1.创建properties文件：

![image-20220411204856260](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220411204856260.png)

2.使用Properties类来获取信息（具体使用IO流章节学过）。

```java
    public void connect05() throws IOException, ClassNotFoundException, SQLException {
        //获取配置信息
        Properties properties = new Properties();
        properties.load(new FileReader("src//mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String dirver = properties.getProperty("dirver");

        //注册驱动
        Class.forName(dirver);
        //连接
        Connection connection = DriverManager.getConnection(url, user, password);
        //操作
        System.out.println(connection);
        //关闭连接
        connection.close();
    }
```



#### ResultSet

![image-20220412153737343](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220412153737343.png)



```java
public void connection() throws IOException, ClassNotFoundException, SQLException {
    //获取配置信息
    Properties properties = new Properties();
    properties.load(new FileReader("src//mysql.properties"));
    String user = properties.getProperty("user");
    String password = properties.getProperty("password");
    String driver = properties.getProperty("driver");
    String url = properties.getProperty("url");
    //注册驱动，获取连接
    Class.forName(driver);
    Connection connection = DriverManager.getConnection(url, user, password);

    //操作
    Statement statement = connection.createStatement();
    String sql = "select * from emp";
    
    ResultSet resultSet = statement.executeQuery(sql);//dml语句执行executeUpdate()方法，查询执行executeQuery()方法
    
    while (resultSet.next()){	//循环输出
        int id = resultSet.getInt(1);
        String ename = resultSet.getString(2);
        int sex = resultSet.getInt(3);
        String job = resultSet.getString(4);
        double salary = resultSet.getDouble(5);
        int deptno = resultSet.getInt(6);
        System.out.println(id+" "+ename+" "+sex+" "+job+" "+salary+" "+deptno);
    }

    //关闭连接
    resultSet.close();
    statement.close();
    connection.close();
}
```

- ResultSet是接口。因为引入了mysql的jar包（该数据库公司提供的），里面实现了该接口。
- **当语句  Connection connection = DriverManager.getConnection(url, user, password) 执行后**，Connection接口类型的变量connection得到的是它的实现类 JDBC4Connection（ConnectionImpl的子类）。[其中实现类是由mysql的jar包提供的]

- **java.sql**：是java公司包，接口为主，不实现。 **com.mysql.jdbc:** 是mysql厂商提供的具体实现。



#### Statement

用于执行静态SQL语句并返回其生成的结果的对象。

```Java
ResultSet resultSet = statement.executeQuery(/*sql语句*/);
```

**介绍：**

![image-20220412161226724](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220412161226724.png)

**SQL注入危险示例：**

（该例子这样输入，可以查询所有用户，因为有恒成立条件）

![image-20220412161821036](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220412161821036.png)

**PreparedStatement使用**：

![image-20220412165255724](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220412165255724.png)

```Java
@Test
public void connection() throws Exception {
    //获取配置信息
    Properties properties = new Properties();
    properties.load(new FileReader("src//mysql.properties"));
    String user = properties.getProperty("user");
    String password = properties.getProperty("password");
    String driver = properties.getProperty("driver");
    String url = properties.getProperty("url");
    //注册驱动，获取连接
    Class.forName(driver);
    Connection connection = DriverManager.getConnection(url, user, password);

    //操作
    int sex = 1;
    double salary = 10000;
   	/*
		1.SQL语句使用占位符 ？
   		2.使用connect调用获取prepareStatement，需要提前给sql语句
   		3.使用prepareStatement.setXXX()的方法，设置每个占位符的具体值。第一个参数是占位符序号，第二个是具体参数。
   		4.然后直接preparedStatement.executeQuery的方法，执行语句。无需在提供SQL语句
   	*/
    String sql = "select * from emp where sex = ? and salary=? ";
    java.sql.PreparedStatement preparedStatement = connection.prepareStatement(sql);
    preparedStatement.setInt(1,sex);
    preparedStatement.setDouble(2,salary);
    ResultSet resultSet = preparedStatement.executeQuery();

    while (resultSet.next()){
        int id = resultSet.getInt(1);
        String ename = resultSet.getString(2);
        System.out.println(id+" "+ename);
    }

    //关闭连接
    resultSet.close();
    preparedStatement.close();
    connection.close();

}
```



#### 小结

常用API的梳理：

![image-20220412170226981](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220412170226981.png)

![image-20220412170643116](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220412170643116.png)
