---
title: JDBC编程
tags: Java
---

### 一、JDBC简介

JDBC(Java Database Connectivity):JAVA数据库连接，是可以执行sql语句的一套API

程序可通过JDBC连接到关系数据库，用sql对数据进行增删改查

通过JDBC开发的数据库应用可跨平台

数据库驱动程序：JDBC与数据库之间的转换层，负责将JDBC调用映射成特定的数据库调用

### 二、JDBC编程

#### 1.常用类和接口

DriverManager：用于管理JDBC驱动的服务类，用于获取Connection对象

Connection：数据库连接对象，每个Connection代表一个连接会话。它是一个接口，可通过Statement，Preparedstatement来实例化，获取其对象

Statement：用于执行sql的工具接口

PreparedStatement：预编译的Statement对象，是Statement子接口，可以预编译sql语句，允许sql语句含参，方便后来修改也可避免每次都编译sql语句，性能好。

Statement和PreparedStatement都有execute()，executeUpdate()，executeQuery()，返回查询的结果集，executeUpdate()可执行DML语句，返回受影响的行数，executeQuery()可以执行DDL语句，返回结果集对象，所以一般不用execute()方法，返回的是ResultSet结果集。

ResultSet：结果集对象，包含很多查询结果的方法，可以根据列名或列索引来获取结果，也有很多种方法来移动记录指针，最常用的是next()方法。

#### 2.步骤(以MySQL为例)

```java
Connection conn = null;
PreparedStatement pstmt = null;
ResultSet rs = null;
```

##### (1).加载数据库驱动

```java
Class.forName("com.mysql.cj.jdbc.Driver");
```

##### (2).获取数据库连接

```java
conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC","root", "password");
```

##### (3).通过Connection创建PreparedStatement对象

```java
pstmt = conn.prepareStatement(sql);
```

##### (4).执行sql语句

```java
rs = pstmt.executeQuery();
result = pstmt.executeUpdate;
```

### 三、模板

这些语句都可以被封装成单独的函数进行使用，更加方便，不用每次再进行定义。

下面是DAO模式分层后，最底层与数据库交互的BaseDao类，方便再使用时拷贝，

连接数据库还需要一个.property文件，里面记录了驱动，数据库位置，用户和密码，每次使用只需要改这里就可以

```java
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/test?useSSL=false&serverTimezone=UTC
user=root
password=123456
```

```java
/**
 * 数据库连接与关闭工具类。
 */
public class BaseDao {
	public static String DRIVER; // 数据库驱动

	public static String URL ; // url

	public static String DBNAME; // 数据库用户名

	public static String DBPASS; // 数据库密码
	
	Connection conn = null;// 数据连接对象
	
	static{//静态代码块,在类加载的时候执行
		init();
	}
	
	/**
	 * 初始化连接参数,从配置文件里获得
	 */
		public static void init(){
			Properties params=new Properties();
			String configFile = "database.properties";//配置文件路径
			//加载配置文件到输入流中
			InputStream is=BaseDao.class.getClassLoader().getResourceAsStream(configFile);
			
			try {
				//从输入流中读取属性列表
				params.load(is);
			} catch (IOException e) {
				e.printStackTrace();
			}
			//根据指定的获取对应的值
			DRIVER=params.getProperty("driver");
			URL=params.getProperty("url");
			DBNAME=params.getProperty("user");
			DBPASS=params.getProperty("password");
		}   

	/**
	 * 得到数据库连接
	 * 
	 * @throws ClassNotFoundException
	 * @throws SQLException
	 * @return 数据库连接
	 */
	public Connection getConnection() throws ClassNotFoundException, SQLException {
		Connection conn = null;
		try {
			Class.forName(DRIVER); // 注册驱动
			conn = DriverManager.getConnection(URL, DBNAME, DBPASS); // 获得数据库连接
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return conn; // 返回连接
	}

	/**
	 * 释放资源
	 * 
	 * @param conn
	 *            数据库连接
	 * @param pstmt
	 *            PreparedStatement对象
	 * @param rs
	 *            结果集
	 */
	public void closeAll(Connection conn, PreparedStatement pstmt, ResultSet rs) {

		/* 如果rs不空，关闭rs */
		if (rs != null) {
			try {
				rs.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		/* 如果pstmt不空，关闭pstmt */
		if (pstmt != null) {
			try {
				pstmt.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		/* 如果conn不空，关闭conn */
		if (conn != null) {
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

	}


	/**
	 * 执行SQL语句，可以进行增、删、改的操作，不能执行查询
	 * 
	 * @param sql
	 *            预编译的 SQL 语句
	 * @param param
	 *            预编译的 SQL 语句中的‘？’参数的字符串数组
	 * @return 影响的条数
	 */
	public int executeSQL(String preparedSql, Object[] param) {
		Connection conn = null;
		PreparedStatement pstmt = null;
		int num = 0;

		/* 处理SQL,执行SQL */
		try {
			conn = getConnection(); // 得到数据库连接
			pstmt = conn.prepareStatement(preparedSql); // 得到PreparedStatement对象
			if (param != null) {
				for (int i = 0; i < param.length; i++) {
					pstmt.setObject(i + 1, param[i]); // 为预编译sql设置参数
				}
			}
		
			num = pstmt.executeUpdate(); // 执行SQL语句
		} catch (ClassNotFoundException e) {
			//e.printStackTrace(); // 处理ClassNotFoundException异常
		} catch (SQLException e) {
			//e.printStackTrace(); // 处理SQLException异常
		} finally {
			this.closeAll(conn, pstmt, null);
		}
		return num;
	}
}

```

