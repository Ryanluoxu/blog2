---
title: 学习 Spring Boot - 2
date: 2017-12-25 11:24:17
categories: SpringBoot
tags: [Spring, Spring Boot, beginner, PostgreSQL, Rest, JPA, Spring Data JPA]
---

**声明：本文为 ryanluoxu 原创文章，欢迎转载，请在明显位置注明出处。**

在《学习 Spring Boot - 1》里，用 Service 虚拟了用户的数据。 这篇博客接着上一篇往下，介绍如何接入 PostgreSQL 数据库。

通常连接数据库，从 Service 层开始，要经过 DAO，DAO 里面需要调用已经完成 JDBC 连接设定的 DBUtil，创建了 connection 之后，通过执行 query 来传递数据。在 SpringBoot 里面，这一部分内容被预设在 Spring Data JPA 里面，通过调用 CrudRepository 即可。思路如下：
```
Rest Client --> RestController | Service | Repository --> PostgreSQL (DBName=Demo; Table=users)
```
根据这个思路，需要修改或者添加：
- SpringBoot 的设定
- 创建 PostgreSQL 数据库 
- 修改 User 类
- 创建 UserRepository 接口
- 修改 UserService 类

<!--more-->

# SpringBoot 的设定

## pom.xml
添加 Dependency
- Spring Data JPA
- PostgreSQL

```
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-data-jpa</artifactId>
	    <version>1.5.7.RELEASE</version>
	</dependency>
	
	<dependency>
	    <groupId>org.postgresql</groupId>
	    <artifactId>postgresql</artifactId>
	    <version>42.1.4</version>
	</dependency>
```
## application.properties (in src/main/resources)
添加：
```
spring.datasource.url=jdbc:postgresql://<Host>/<DBName>
spring.datasource.username=<username>
spring.datasource.password=<password>
spring.jpa.generate-ddl=true
```
例如：
```
spring.datasource.url=jdbc:postgresql://postgresql.cfqvncjculpj.ap-southeast-1.rds.amazonaws.com:5432/Demo
spring.datasource.username=admin123
spring.datasource.password=admin123
spring.jpa.generate-ddl=true
```

# 创建 PostgreSQL 数据库
    
## 安装连接 PostgreSQL 数据库
具体参考-> [Database - PostgreSQL on AWS](/2017/12/16/Database-PostgreSQL/)


## 创建表格

```
CREATE TABLE users (
    userId SERIAL,
    userName varchar(20)  PRIMARY KEY,
    nationality varchar(30),
    age int
);
```

## 输入数据
```
INSERT INTO users (userName, nationality, age) VALUES
	('Hazel','Chinese',26),
	('Michael','Australian',32),
	('Melvin','Singaporean',30)
	;
```
效果如下：
![users-table](/images/users-table.png)

# 修改 User 类

**DataModel Class: User.java**
Before:
```
	public class User {
	
		private String userName;
		private int age;
		private String nationality;

		...setters and getters...		
	}
```
After:
```
	@Entity
	@Table(name="users")
	public class User implements Serializable {
	
		@Id		
		private String userName;
		
		@Column(name="age")
		private int age;
		
		@Column(name="nationality")
		private String nationality;
	
		...setters and getters...		
	}
```

- `@Entity` 标明这是一个与数据库对接的类
- `@Table(name="users")` 里面定义了信息所在的表格
- `implements Serializable`
- `@Id` 关键
- `@Column(name="age")`



# 创建 UserRepository 接口
1. 创建 package: io.ryanluoxu.springboot.demo.repository
2. 在下面创建接口类: UserRepository.java, 如下
```
public interface UserRepository extends CrudRepository<User, String>{

}
```

里面包括的方法：
- count()
- delete(T entity)
- deleteAll()
- deleteAll(Iterable<? extends T> entities)
- deleteById(ID id)
- existsById(ID id)
- findAll()
- findAllById(Iterable<ID> ids)
- findById(ID id)
- save(S entity)
- saveAll(Iterable<S> entities)

此处 Id 对应 User 类中的 @Id。
如果需要对 Id 以外的进行搜索，可以仅仅创建 Abstract Method:
```
public ArrayList<User> findByNationality(String nationality);
```
不需要 Implement 类。

# 修改 UserService 类

## UserService.java
1. 删去模拟数据的 Constructor
2. 将 ArrayList 换成 UserRepository
3. 修改调用方法
具体如下：
```
@Service
public class UserService {
	
	@Autowired
	UserRepository userRepository;
	
	public ArrayList<User> getUserList(){
		return (ArrayList<User>) userRepository.findAll();
	}

	public User getUserByName(String name) {
		return userRepository.findOne(name);
	}

	public boolean addUser(User user) {
		User u = userRepository.save(user);
		if (u != null) {
			return true;
		} else {
			System.err.println("Fail to add - " + user.getUserName());
			return false;
		}
	}

	public boolean updateUserByName(User updatedUser, String name) {
		// Spring will take userName from updatedUser
		// if userName exists, update; if not, add new user
		User u = userRepository.save(updatedUser);
		if (u != null) {
			return true;
		} else {
			System.err.println("Fail to update - " + updatedUser.getUserName());
			return false;
		}
	}

	public boolean removeUserByName(String name) {
		try {
			userRepository.delete(name);
			return true;
		} catch (Exception e) {
			System.err.println("Fail to remove user called " + name);	
			return false;
		}
	}
}
```

## Save() 同时用于更新和添加
1. 测试1
	PUT - localhost:8080/users/Melvin
	```
	{
	    "userName": "Grace",
	    "age": 26,
	    "nationality": "Chinese"
	}
	```
	结果添加了新的用户，而没有更改 Melvin 的信息。
2. 测试2
	POST - localhost:8080/users/
	```
	{
	    "userName": "Michael",
	    "age": 27,
	    "nationality": "Chinese"
	}
	```
	结果Michael的信息被更改，而并没有添加新的用户。

这里，添加新的用户和更新用户，用的同一个方法-save(User user)。
区别在于定义 User 时的 @id，以及 CrudRepository<User, String>，都指明了这组数据的 Primary Key。

**所以，Primary Key 不同，即为添加用户。相同，则为更新用户。 **

**更新 UserService.java**
```
	public boolean addUser(User user) {
		// name shouldn't be existed.
		if (userRepository.findOne(user.getUserName()) == null) {
			userRepository.save(user);
			return true;
		} else {
			System.err.println(user.getUserName() + " is existed..");
			return false;
		}
	}

	public boolean updateUserByName(User updatedUser, String name) {
		// name must be the same
		if (updatedUser.getUserName().equals(name)) {
			User u = userRepository.save(updatedUser);
			if (u != null) {
				return true;
			} else {
				System.err.println("Fail to update - " + updatedUser.getUserName());
				return false;
			}
		} else {
			System.err.println("Unable to change the userName for existed user - "  + name);
			return false;
		}
	}
```

** 继续学习 -> [学习 Spring Boot](/categories/SpringBoot)**


=====


Maven to build it as jar
java -jar to run it


actuator
-> / health
managerment.port=

application.properties
[Common application properties](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)