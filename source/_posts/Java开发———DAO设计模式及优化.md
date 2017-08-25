---
title: Java开发———DAO设计模式及优化
date: 2017-08-25 10:00:23
tags: java
---
**什么是DAO？**

 > DAO是Data Access Object 数据访问接口，顾名思义：就是与数据库打交道。夹在业务逻辑与数据资源的中间。

**DAO模式有哪些？**

> DAO模式实际上是两个模式的组合 既Data Accessor 模式和Active Domain Object模式。

**它们的意义和作用**

> Data Accessor模式实现了数据访问和业务逻辑的分离，Active Domain Object 模式实现了业务数据的对象化封装。
以javaWeb中对数据库的为例：
**首先预先准备一个工具类DBUtils.class**

```
public class DBUtils {
	public final static String URL="jdbc:mysql://localhost:3306/mydb";
	public final static String USERNAME="root";//用户名
	public final static String  PASSWORD="*****";//密码
	public final static String DRIVER="com.mysql.jdbc.Driver";
	private DBUtils(){
	}
	//使用静态快加载驱动
	static{
		try {
			Class.forName(DRIVER);
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	//定义一个获取数据库连接的方法
	public static Connection getconnection() {
		Connection connection=null;
		try {
	    connection=DriverManager.getConnection(URL, USERNAME, PASSWORD);
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
			System.out.println("获取连接失败");
		}
		return connection;
		
	}
	public static void close(ResultSet rs,Statement statement,Connection conn){
	   	 if (rs!=null)
				try {
					rs.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			if (statement!=null) {
				try {
					statement.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}	
			}
			if (conn!=null) {
				try {
					conn.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			}}		
```

**接下来我们要来实现Dao模式**        

一个典型的DAO模式有以下几个组件：一个接口，数据传递对象或者领域模型， 
先建立一个类 person.class  数据模型类

```
public class Person {
	private int id;
	private String name;
	private int age;
	private String description;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getDescription() {
		return description;
	}
	public void setDescription(String description) {
		this.description = description;
	}
	public Person( String name, int age, String description) {
		super();
		this.name = name;
		this.age = age;
		this.description = description;
	}
	public Person() {
		super();
		// TODO Auto-generated constructor stub
	}
	@Override
	public String toString() {
		return "person [id=" + id + ", name=" + name + ", age=" + age + ", description=" + description + "]";
	}
```
	
**声明一个接口，数据操作类  PersonDao.class**
> `import java.sql.SQLException;
import java.util.List;
import com.vince.domain.Person;
public interface PersonDao {
	public void add(Person p) throws SQLException;//这里需要抛出异常；否则在下面操作的时候会报错！！！！
	public void update(Person p) throws SQLException;
	public void delete(int id) throws SQLException;
	public Person findById(int id) throws SQLException;
	public List<Person> findAll() throws SQLException;
}
`

**编写接口的实现类  PersonDanImpl.class**

```
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import com.util.db.DBUtils;
import com.util.db.JdbcTemplete;
import com.vince.base.ResultSetHandler;
import com.vince.dao.PersonDao;
import com.vince.domain.Person;

public class PersonDaoImpl implements PersonDao {
private JdbcTemplete jdbcTemolete;
	public PersonDaoImpl() {
		jdbcTemolete=new JdbcTemplete();
}
	@Override
	public void add(Person p) throws SQLException {
		Connection connection=null;
		PreparedStatement ps=null;
		String sql="insert into person(name,age,description)values(?,?,?)";
		try {
		connection=DBUtils.getconnection();
		ps=connection.prepareStatement(sql);
		ps.setString(1, p.getName());
		ps.setInt(2, p.getAge());
		ps.setString(3, p.getDescription());
		ps.executeUpdate();
	} catch (SQLException e) {
		
		throw new SQLException("数据库添加异常");
	}finally {
		DBUtils.close(null, ps, connection);
	}
	
		}
@Override
	public void update(Person p) throws SQLException {
	Connection connection=null;
		PreparedStatement ps=null;
		String sql="update person set name=?,age=?,description=? where id=?";
		try {
		connection=DBUtils.getconnection();
		ps=connection.prepareStatement(sql);
		ps.setString(1, p.getName());
		ps.setInt(2, p.getAge());
		ps.setString(3, p.getDescription());
		ps.setInt(4, p.getId());
		ps.executeUpdate();	
	} catch (SQLException e) {
			throw new SQLException("数据库更新异常");
	}finally {
		DBUtils.close(null, ps, connection);
	}	
	}
	@Override
	public void delete(int id) throws SQLException {
		Connection connection=null;
		PreparedStatement ps=null;
		String sql="delete from person where id=?";
		try {
		connection=DBUtils.getconnection();
		ps.setInt(1,id);
		ps.executeUpdate();
		
	} catch (SQLException e) {
		e.printStackTrace();
		throw new SQLException("数据库删除异常");
	}finally {
		DBUtils.close(null, ps, connection);
	}
	}

@Override
	public List<Person> findAll() throws SQLException {
		 Connection connection=null;
		   PreparedStatement ps=null;
		   ResultSet rs=null;
		   Person p=null;
		   List<Person> persons=new ArrayList<Person>();
			String sql="selectid,name,age,description from person";
			try {
				connection=DBUtils.getconnection();
				ps=connection.prepareStatement(sql);
				rs=ps.executeQuery();
				while(rs.next()){
					p=new Person();
					p.setId(rs.getInt(1));
					p.setName(rs.getString(2));
					p.setAge(rs.getInt(3));
					p.setDescription(rs.getString(4));
					persons.add(p);
					}
				} catch (SQLException e) {
				// TODO: handle exception
				throw new SQLException("查询所有数据异常");
			}finally {
				DBUtils.close(rs, ps, connection);
			}
			return persons;	}
}
```
**上面就是DAO模式的实现过程。这种DAO设计模式主要是可以让我们写的代码，更加简洁，冗余小，实现了软件设计设计模式中的一条规则：高内聚，低耦合；下面呢？我们来思考能不能继续优化DAO设计模式的实现，**

> 新建一个类，JdbcTemplete .class  来再做个一个提炼 抽象；
>`import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import com.vince.base.ResultSetHandler;
public class JdbcTemplete {
	public int update(String sql,Object ...args){
	Connection connection=null;
	PreparedStatement ps=null;
	try {
		connection=DBUtils.getconnection();
		ps=connection.prepareStatement(sql);
		if (args!=null) {
			for(int i=0;i<args.length;i++){
				ps.setObject(i+1, args[i]);
			}
		}
		return ps.executeUpdate();
	} catch (SQLException e) {
		e.printStackTrace();
		return 0;
	}finally {
		DBUtils.close(null, ps, connection);
	}
	}
	public Object query(String sql,ResultSetHandler handler,Object...args){
		Connection connection=null;
		PreparedStatement ps=null;
		ResultSet rs=null;
		try {
			connection=DBUtils.getconnection();
			connection.prepareStatement(sql);
			if (args!=null) {
				for(int i=0;i<args.length;i++){
					ps.setObject(i+1, args[i]);
				}
			}
			rs=ps.executeQuery();
				return	handler.doHandler(rs);
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
			return null;
		}
	}
}
`
所有类的父类是Objects  有多个参数， 所有我们使用Object ...args；
接下来在PersonDaoImpl.class类中，我们就可以使用我们提取处在的抽象类了 嘻嘻~ 是不是代码简单多了~~ 向对于数据库的增删 更新的操作都可以使用类似的方法，

```
@Override
	public void add(Person p) throws SQLException {
		String sql="insert into person(name,age,description)values(?,?,?)";
		jdbcTemolete.update(sql, p.getName(),p.getDescription());	
	}
```
 对于一个需要从数据库查询的操作，在查询的操作，需要用道 ResultSet这个方法的
  我们这里定义一个接口ResultSetHandler.class；
  

```
import java.sql.ResultSet;
import java.sql.SQLException;

public interface ResultSetHandler {
	public Object doHandler(ResultSet rs)throws SQLException;
}

```


**在JdbcTemplete.class中的query这个方法中 就需要用的这个接口
在PersonDaoImpl.class中这个查询方法，我们就可以这样写了**

```
@Override
	public Person findById(final int id) throws SQLException {
	  
		String sql="select name,age,description from person where id=?";
	  return  (Person) jdbcTemolete.query(sql, new ResultSetHandler() {
		
		@Override
		public Object doHandler(ResultSet rs)throws SQLException {
			 Person p=null;
				if(rs.next()){
					p=new Person();
					p.setId(id);
					p.setName(rs.getString(1));
					p.setAge(rs.getInt(2));
	 				p.setDescription(rs.getString(3));			                          }
			return p;
		}
	},id);
	}
```
最后 就是弄一个main 方法 来使用我们定义好 对数据操作的方法喽


**有什么错误需要改正欢迎指出，共同学习**
     **有什么错误需要改正欢迎指出，共同学习**