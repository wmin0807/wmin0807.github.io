---
layout: post
title: Git提交错误后如何回退

---

#### 使用Spring JDBC方式访问数据库

##### 为什么

首先看一下使用最为原始的JDBC向数据库中插入一条记录：

```
	private static final String INSERT_STUDENT = "insert into 		spitter (username, password) values (?,?)";
	
	private DataSource dataSource;
	
	public void addStudent(Spitter stu) {
		Connection conn = null;
		PreparedStatement stmt = null;
		try {
			conn = dataSource.getConnection();
			stmt = conn.prepareStatement(INSERT_STUDENT);
			stmt.setString(1, stu.getUserName());
			stmt.setString(2, stu.getPassWord());
			stmt.execute();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				if (stmt != null) {
					stmt.close();
				}
				if (conn != null) {
					conn.close();
				}
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
```
传统的最原始的基于JDBC访问数据库的方式是非常麻烦的，需要写下很多与数据访问无关的代码，主要在于对异常的捕捉。SQLException不会告诉你任何有用的信息，但是你必须捕获。SQLException被认为是数据访问的通用异常，对于所有的数据访问问题都会抛出SQLException，而不是对每种可能的问题都会有不同的异常类型。

所以Spring的好处就来了：

* Spring 提供了平台无关的异常体系，这些异常不会与任何一个Jdbc或ORM框架耦合，同时也具有描述性（加粗）.Spring的异常体系都是继承自DataAccessException ，这个DataAccessException的特殊之处在于是一个非检查性异常，也就是没有必要捕获Spring所抛出的数据访问异常。
* Spring提供了数据访问的模板，把这些不必要的操作封装起来，我们在使用的时候就只关心数据访问部分。Spring提供了很多模板，JdbcTemplate/HibernateTemplate/JpaTemplate ...

### 如何用

主要介绍如何使用Spring的JdbcTemplate 来访问数据库。

*  首先配置数据源，这里使用的dbcp的数据源，当然也可以选用其他的数据源：

```
<bean id="dataSource"
  	class="org.apache.commons.dbcp.BasicDataSource">
  	<property name="driverClassName" value="com.mysql.jdbc.Driver" />  
    <property name="url" value="jdbc:mysql://localhost:3306/spring" />  
    <property name="username" value="root" />
    <property name="password" value="root"/>
    <property name="initialSize" value="5" />
    <property name="maxActive" value="10" />
  </bean>
```
*  编写DAO文件，当然这里就是StudentDAO，根据Spring对数据访问的哲学，DAO 文件是一个接口文件，只是提供基本的饿数据访问功能，并不是做出实现，这样的好处在于可以随意的切换持久层的框架。在这里只是简单地写了一个插入的方法：

```
public interface StudentDAO {
	public void addStudent(Student stu);
}
```
*  编写DAO的实现类，这里就是JdbcStudentDAO，这个实现类里面首先是继承了JdbcDaoSupport，因为这个JdbcDaoSupport类里面就直接包含一个jdbcTemplate的属性，可以直接使用getJdbcTemplate得到jdbcTemplate。代码如下：

```
public class JdbcStudentDAO extends JdbcDaoSupport implements StudentDAO {
	public void addStudent(Student stu) {
		getJdbcTemplate().update("insert into student (username, password) values (?,?)", 
				stu.getUserName(), stu.getPassWord());
	}

}
```

---

根据以上三步就能实现使用Spring的jdbcTemplate 来实现数据访问。
