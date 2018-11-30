# Mybatis-Plus简介

## 介绍

MyBatis-Plus(简称 MP),是一个 MyBatis 的增强工具包，只做增强不做改变. 为简化开发工作、提高生产率而生
我们的愿景是成为 Mybatis 最好的搭档，就像 魂斗罗 中的 1P、 2P，基友搭配，效率翻倍。 

PS:MybatisPlus 是基于Mybatis 来实现其功能的

## 代码及文档发布地址 

官方地址:

http://mp.baomidou.com
代码发布地址:
Github: https://github.com/baomidou/mybatis-plus
Gitee: https://gitee.com/baomidou/mybatis-plus
文档发布地址:
http://mp.baomidou.com/#/?id=%E7%AE%80%E4%BB%8B 



# 2.MP 集成 Mybatis

## 怎么开始使用MP

在配置中替换一个地方：

```xml
<!--  配置SqlSessionFactoryBean 
  Mybatis提供的: org.mybatis.spring.SqlSessionFactoryBean
  MP提供的:com.baomidou.mybatisplus.spring.MybatisSqlSessionFactoryBean
  -->
<bean id="sqlSessionFactoryBean" class="com.baomidou.mybatisplus.spring.MybatisSqlSessionFactoryBean"> 
</bean>
```

## MP 操作

###通用 CRUD

1) 提出问题:
假设我们已存在一张 tbl_employee 表，且已有对应的实体类 Employee，实现
tbl_employee 表的 CRUD 操作我们需要做什么呢？
2) 实现方式:
基于 Mybatis
​	需要编写 EmployeeMapper 接口，并手动编写 CRUD 方法
​	提供 EmployeeMapper.xml 映射文件，并手动编写每个方法对应的 SQL 语句.
基于 MP
​	**只需要创建 EmployeeMapper 接口, 并继承 BaseMapper 接口**.这就是使用 MP
​	需要完成的所有操作，甚至不需要创建 SQL 映射文件。 

### 插入

```java
//初始化Employee对象
Employee employee  = new Employee();
employee.setLastName("MP");
employee.setEmail("mp@atguigu.com");
//employee.setGender(1);
//employee.setAge(22);
employee.setSalary(20000.0);
//插入到数据库   
// insert方法在插入时， 会根据实体类的每个属性进行非空判断，只有非空的属性对应的字段才会出现到SQL语句中
//Integer result = employeeMapper.insert(employee);  

//insertAllColumn方法在插入时， 不管属性是否非空， 属性所对应的字段都会出现到SQL语句中. 
Integer result = employeeMapper.insertAllColumn(employee);

System.out.println("result: " + result );

//获取当前数据在数据库中的主键值
Integer key = employee.getId();
System.out.println("key:" + key );
```

#### 注意点

1

需要说明的是`insert` 方法在插入的时候回判空处理，所以如果没有设值，比如`gender,age`属性被注释了，那么在插入的时候sql语句是如下：

```
INSERT INTO tbl_employee ( last_name, email ) VALUES ( ?, ? )
```

但是如果使用`insertAllColumn` ,哪怕有些字段没有赋值，执行操作的sql语句如下：

```
INSERT INTO tbl_employee ( last_name,email,gender,age ) VALUES ( ?,?,?,? )
```

2

MP在执行插入操作的时候，会把主键值返回，并且赋值到Bean的主键字段，这也是下面这段代码的使用原因

```java
//获取当前数据在数据库中的主键值
Integer key = employee.getId();
System.out.println("key:" + key );
```





