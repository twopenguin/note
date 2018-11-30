# Mybatis使用

## 基本使用

### 配置文件

####主配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//ibatis.apache.org//DTD Config 3.0//EN"
        "http://ibatis.apache.org/dtd/ibatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="defaultStatementTimeout" value="60"/>
    </settings>

    <plugins>
        <plugin interceptor="com.zcd.mybatis.plugin.SqlPrintInterceptor">
        </plugin>
    </plugins>

    <environments default="company">
        <environment id="home">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/springbootdb"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>

        <environment id="company">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/springbootdb"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>


    <mappers>
        <mapper resource="mapper/CityMapper.xml"></mapper>
    </mappers>

</configuration>
```

#### Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.zcd.mybatis.mapper.CityMapper">
	<resultMap id="BaseResultMap" type="com.zcd.mybatis.pojo.City">
		<result column="id" property="id" />
		<result column="province_id" property="provinceId" />
		<result column="city_name" property="cityName" />
		<result column="description" property="description" />
	</resultMap>

	<parameterMap id="City" type="com.zcd.mybatis.pojo.City"/>

	<sql id="Base_Column_List">
		id, province_id, city_name, description
	</sql>

	<!--<select id="findByName" resultMap="BaseResultMap" parameterType="java.lang.String">-->
		<!--select-->
		<!--<include refid="Base_Column_List" />-->
		<!--from city-->
		<!--where city_name = #{cityName}-->
	<!--</select>-->

	<select id="getAll" resultMap="BaseResultMap">
		SELECT
		<include refid="Base_Column_List" />
		from city
	</select>

</mapper>
```

### Mapper接口

```java
public interface CityMapper {

    List<City> getAll();
//    City findByName(String cityName);
}
```

### 数据模型

```java
public class City {

    /**
     * 城市编号
     */
    private Long id;

    /**
     * 省份编号
     */
    private Long provinceId;

    /**
     * 城市名称
     */
    private String cityName;

    /**
     * 描述
     */
    private String description;
    //getter，setter
}
```

### 程序入口

```java
public class Entrance {

    public static void main(String[] args) {
        try {
            SqlSession session = openSession();
            CityMapper mapper = session.getMapper(CityMapper.class);
            List<City> cities = mapper.getAll();
            System.out.println(cities);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession openSession() throws IOException {
        String resource = "Configuration.xml";
        Reader reader = Resources.getResourceAsReader(resource);
//         sqlSessionFactory= new SqlSessionFactoryBuilder().build(reader);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        return sqlSession;
    }
}
```

# 源码

## 总览

### 核心组件

  从MyBatis代码实现的角度来看，MyBatis的主要的核心部件有以下几个：

> SqlSession           作为MyBatis工作的主要顶层API，表示和数据库交互的会话，完成必要数据库增删改查功能
>
> Executor             MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护
>
> StatementHandler  封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数、将Statement结果集转换成List集合。
>
> ParameterHandler  负责对用户传递的参数转换成JDBC Statement 所需要的参数，
>
> ResultSetHandler   负责将JDBC返回的ResultSet结果集对象转换成List类型的集合；
>
> TypeHandler         负责java数据类型和jdbc数据类型之间的映射和转换
>
> MappedStatement  MappedStatement维护了一条<select|update|delete|insert>节点的封装， 
>
> SqlSource           负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回
>
> BoundSql            表示动态生成的SQL语句以及相应的参数信息
>
> Configuration       MyBatis所有的配置信息都维持在Configuration对象之中。

基本流程就是如下的，下面我们一步一步的看源码

```java
SqlSession session = openSession();
CityMapper mapper = session.getMapper(CityMapper.class);
List<City> cities = mapper.getAll();
```



## Configuration

所有的配置都是存在在这个配置文件里面

### 重要数据

#### MappedStatement  放哪儿，什么时候生效

`Configuration` 类中有如下字段：

```java
protected final Map<String, MappedStatement> mappedStatements = new StrictMap<MappedStatement>("Mapped Statements collection");
```



###解析主配置文件

```java
/**
 * XMLConfigBuilder 类
 */
public Configuration parse() {
    if (parsed) {
      throw new BuilderException("Each XMLConfigBuilder can only be used once.");
    }
    parsed = true;
    //具体代码在下面
    parseConfiguration(parser.evalNode("/configuration"));
    return configuration;
  }

  private void parseConfiguration(XNode root) {
    try {
      propertiesElement(root.evalNode("properties")); //issue #117 read properties first
      typeAliasesElement(root.evalNode("typeAliases"));
      pluginElement(root.evalNode("plugins"));
      objectFactoryElement(root.evalNode("objectFactory"));
      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
      settingsElement(root.evalNode("settings"));
      environmentsElement(root.evalNode("environments")); // read it after objectFactory and objectWrapperFactory issue #631
      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
      typeHandlerElement(root.evalNode("typeHandlers"));
      mapperElement(root.evalNode("mappers"));
    } catch (Exception e) {
      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
    }
  }
```

### environment节点解析

```java
  private void environmentsElement(XNode context) throws Exception {
    if (context != null) {
      if (environment == null) {
        environment = context.getStringAttribute("default");
      }
      for (XNode child : context.getChildren()) {
        String id = child.getStringAttribute("id");
        if (isSpecifiedEnvironment(id)) {
          //解析事务
          TransactionFactory txFactory = transactionManagerElement(child.evalNode("transactionManager"));
          //解析数据源
          DataSourceFactory dsFactory = dataSourceElement(child.evalNode("dataSource"));
          DataSource dataSource = dsFactory.getDataSource();
          Environment.Builder environmentBuilder = new Environment.Builder(id)
              .transactionFactory(txFactory)
              .dataSource(dataSource);
          configuration.setEnvironment(environmentBuilder.build());
        }
      }
    }
  }
```



### 解析Mapper文件

```java
// XMLMapperBuilder 中负责 Mapper的解析
public void parse() {
if (!configuration.isResourceLoaded(resource)) {
  configurationElement(parser.evalNode("/mapper"));
  configuration.addLoadedResource(resource);
  bindMapperForNamespace();
}

parsePendingResultMaps();
parsePendingCacheRefs();
parsePendingStatements();
}
```





## CityMapper mapper = session.getMapper(CityMapper.class);

我们看下面的这段代码：

```java
SqlSession session = openSession();
CityMapper mapper = session.getMapper(CityMapper.class);
```

上面的例子中`CityMapper`是一个接口，并且我们没有定义实现类：它是通过 `SqlSession` 的 `getMapper` 方法来获取的，调试发现 `mapper` 的实际类型是

```java
org.apache.ibatis.binding.MapperProxy
```

看看这个类的定义

```java
public class MapperProxy<T> implements InvocationHandler, Serializable {
  private static final long serialVersionUID = -6424540398559729838L;
  private final SqlSession sqlSession;
  private final Class<T> mapperInterface;
  private final Map<Method, MapperMethod> methodCache;
}
```

发现实现了`InvocationHandler` 接口，这是一个代理类，我们应该想到它是使用了JDK的动态代理生成的这个类：

### 具体流程

1，从`DefaultSqlSession` 的如下方法开始：

```java
public <T> T getMapper(Class<T> type) {
	return this.configuration.getMapper(type, this);
}
```

2.`Configuration`

此类有一个类型为`MapperRegistry` 的属性

```java
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
	return this.mapperRegistry.getMapper(type, sqlSession);
}
```

3.`MapperRegistry` 

一个接口对应一个`MapperProxyFactory`

```java
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
	MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory)this.knownMappers.get(type);
	if (mapperProxyFactory == null) {
		throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
	} else {
		try {
          	// 3
			return mapperProxyFactory.newInstance(sqlSession);
		} catch (Exception var5) {
			throw new BindingException("Error getting mapper instance. Cause: " + var5, var5);
		}
	}
}
```

4.`MapperProxyFactory`

```java
public T newInstance(SqlSession sqlSession) {
	MapperProxy<T> mapperProxy = new MapperProxy(sqlSession, this.mapperInterface, this.methodCache);
	return this.newInstance(mapperProxy);
}

protected T newInstance(MapperProxy<T> mapperProxy) {
  	//4
	return Proxy.newProxyInstance(this.mapperInterface.getClassLoader(), new Class[]{this.mapperInterface}, mapperProxy);
}
```

上面代码标注的4的地方可明显看出是创建一个代理类，这样我们就得到了CityMapper的实例

## List<City> cities = mapper.getAll();

有上面一节我们知道，mapper是`MapperProxy` 的实例，由动态代理的知识可以知道，mapper的所以方法调用都会走`MapperProxy` 类的`invoke` 方法，下面我们来看看这个方法

```java
@Override
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
//代理以后，所有Mapper的方法调用时，都会调用这个invoke方法
//并不是任何一个方法都需要执行调用代理对象进行执行，如果这个方法是Object中通用的方法（toString、hashCode等）无需执行
if (Object.class.equals(method.getDeclaringClass())) {
  try {
	return method.invoke(this, args);
  } catch (Throwable t) {
	throw ExceptionUtil.unwrapThrowable(t);
  }
}
//这里优化了，去缓存中找MapperMethod
final MapperMethod mapperMethod = cachedMapperMethod(method);
//执行
return mapperMethod.execute(sqlSession, args);
}
```

上面的这个方法，前面是判断此方法是否是`Object` 的本身的方法，如`toString（）` 之类，是的话就排除，重点是：

```java
return mapperMethod.execute(sqlSession, args);
```

### 具体流程

1.创建一个`MapperMethod` 对象



```java
//这里优化了，去缓存中找MapperMethod
final MapperMethod mapperMethod = cachedMapperMethod(method);
```

点进去看看：

```java
//去缓存中找MapperMethod
private MapperMethod cachedMapperMethod(Method method) {
MapperMethod mapperMethod = methodCache.get(method);
if (mapperMethod == null) {
  //找不到才去new
  mapperMethod = new MapperMethod(mapperInterface, method, sqlSession.getConfiguration());
  methodCache.put(method, mapperMethod);
}
return mapperMethod;
}
```

重点看看`MapperMethod` 的构造方法：

```java
public MapperMethod(Class<?> mapperInterface, Method method, Configuration config) {
  this.command = new SqlCommand(config, mapperInterface, method);
  this.method = new MethodSignature(config, method);
}
```

2.调用`MapperMethod` 的`execute` 方法：

```java
public Object execute(SqlSession sqlSession, Object[] args) {
Object result;
if (SqlCommandType.INSERT == command.getType()) {
  Object param = method.convertArgsToSqlCommandParam(args);
  result = rowCountResult(sqlSession.insert(command.getName(), param));
} else if (SqlCommandType.UPDATE == command.getType()) {
  Object param = method.convertArgsToSqlCommandParam(args);
  result = rowCountResult(sqlSession.update(command.getName(), param));
} else if (SqlCommandType.DELETE == command.getType()) {
  Object param = method.convertArgsToSqlCommandParam(args);
  result = rowCountResult(sqlSession.delete(command.getName(), param));
} else if (SqlCommandType.SELECT == command.getType()) {
  if (method.returnsVoid() && method.hasResultHandler()) {
	executeWithResultHandler(sqlSession, args);
	result = null;
  } else if (method.returnsMany()) {
	result = executeForMany(sqlSession, args);
  } else if (method.returnsMap()) {
	result = executeForMap(sqlSession, args);
  } else {
	Object param = method.convertArgsToSqlCommandParam(args);
	result = sqlSession.selectOne(command.getName(), param);
  }
} else {
  throw new BindingException("Unknown execution method for: " + command.getName());
}
if (result == null && method.getReturnType().isPrimitive() && !method.returnsVoid()) {
  throw new BindingException("Mapper method '" + command.getName() 
	  + " attempted to return null from a method with a primitive return type (" + method.getReturnType() + ").");
}
return result;
}
```

因为我们的`getAll`方法是一个查询方法，所以进入`SqlCommandType.SELECT` 的分支，即如下的代码:

```java
if (method.returnsVoid() && method.hasResultHandler()) {
	executeWithResultHandler(sqlSession, args);
	result = null;
} else if (method.returnsMany()) {
	result = executeForMany(sqlSession, args);
} else if (method.returnsMap()) {
	result = executeForMap(sqlSession, args);
} else {
	Object param = method.convertArgsToSqlCommandParam(args);
	result = sqlSession.selectOne(command.getName(), param);
}
```

里面的细节我们暂时不看，**结论就是**内部的实现都是使用`SqlSession`来查询数据的，类似如下：

```java
result = sqlSession.selectOne(command.getName(), param);
```

## SqlSession如何查数据

1.委托给CachingExecutor 的query方法

```java
public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
try {
  MappedStatement ms = configuration.getMappedStatement(statement);
  //executor的定义如下：
  List<E> result = executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
  return result;
} catch (Exception e) {
  throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
} finally {
  ErrorContext.instance().reset();
}
}

private Executor executor;
```

CacheExecutor 的query 方法又委托给 SimpleExecutor的query 方法，其实就是`BaseExecutor`的query方法

```java
public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql)
  throws SQLException {
	Cache cache = ms.getCache();
	if (cache != null) {
	  flushCacheIfRequired(ms);
	  if (ms.isUseCache() && resultHandler == null) {
		ensureNoOutParams(ms, parameterObject, boundSql);
		@SuppressWarnings("unchecked")
		List<E> list = (List<E>) tcm.getObject(cache, key);
		if (list == null) {
		  list = delegate.<E> query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
		  tcm.putObject(cache, key, list); // issue #578. Query must be not synchronized to prevent deadlocks
		}
		return list;
	  }
	}
  	// delegate 是 SimpleExecutor的实例
	return delegate.<E> query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
}
```



`BaseExecutor`

```java
  public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
    ErrorContext.instance().resource(ms.getResource()).activity("executing a query").object(ms.getId());
    if (closed) throw new ExecutorException("Executor was closed.");
    if (queryStack == 0 && ms.isFlushCacheRequired()) {
      clearLocalCache();
    }
    List<E> list;
    try {
      queryStack++;
      list = resultHandler == null ? (List<E>) localCache.getObject(key) : null;
      if (list != null) {
        handleLocallyCachedOutputParameters(ms, key, parameter, boundSql);
      } else {
        // Next
        list = queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);
      }
    } finally {
      queryStack--;
    }
    if (queryStack == 0) {
      for (DeferredLoad deferredLoad : deferredLoads) {
        deferredLoad.load();
      }
      deferredLoads.clear(); // issue #601
      if (configuration.getLocalCacheScope() == LocalCacheScope.STATEMENT) {
        clearLocalCache(); // issue #482
      }
    }
    return list;
  }
```

`BaseExecutor`

```java
  private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
    List<E> list;
    localCache.putObject(key, EXECUTION_PLACEHOLDER);
    try {
      //Next
      list = doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
    } finally {
      localCache.removeObject(key);
    }
    localCache.putObject(key, list);
    if (ms.getStatementType() == StatementType.CALLABLE) {
      localOutputParameterCache.putObject(key, parameter);
    }
    return list;
  }
```

`BaseExecutor`

```java
  public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
    Statement stmt = null;
    try {
      Configuration configuration = ms.getConfiguration();
      StatementHandler handler = configuration.newStatementHandler(wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
      stmt = prepareStatement(handler, ms.getStatementLog());
      //Next
      return handler.<E>query(stmt, resultHandler);
    } finally {
      closeStatement(stmt);
    }
  }
```

`RoutingStatementHandler`

```java
  public <E> List<E> query(Statement statement, ResultHandler resultHandler) throws SQLException {
    return delegate.<E>query(statement, resultHandler);
  }
```

`PreparedStatementHandler`

```java
  public <E> List<E> query(Statement statement, ResultHandler resultHandler) throws SQLException {
    PreparedStatement ps = (PreparedStatement) statement;
    ps.execute();
    return resultSetHandler.<E> handleResultSets(ps);
  }
```

这儿就需要解释一下：

PreparedStatement 就是JDBC操作中的那个 Statement，在它调用execute 方法的时候，已经把值也就是ResultSet查出来了，`resultSetHandler.<E> handleResultSets(ps)` 这行代码是用来包装结果集转为我们需要的对象的



## 事务

### 事务的配置：

如下：可以在`environment`的 `transactionManager` 配置，有`JDBC` 和`MANAGED` 两种选择

```xml
        <environment id="home">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/springbootdb"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
```

### 事务配置如何生效

我们知道所有的配置都是集中在`Configuration` 这个类中，并且整个项目只有一个这个实例，在这个类实例化的时候有如下操作：

```java
    public Configuration() {
        //注册更多的类型别名，至于为何不直接在TypeAliasRegistry里注册，还需进一步研究
        typeAliasRegistry.registerAlias("JDBC", JdbcTransactionFactory.class);
        typeAliasRegistry.registerAlias("MANAGED", ManagedTransactionFactory.class);

        typeAliasRegistry.registerAlias("JNDI", JndiDataSourceFactory.class);
        typeAliasRegistry.registerAlias("POOLED", PooledDataSourceFactory.class);
        typeAliasRegistry.registerAlias("UNPOOLED", UnpooledDataSourceFactory.class);

        typeAliasRegistry.registerAlias("PERPETUAL", PerpetualCache.class);
        typeAliasRegistry.registerAlias("FIFO", FifoCache.class);
        typeAliasRegistry.registerAlias("LRU", LruCache.class);
        typeAliasRegistry.registerAlias("SOFT", SoftCache.class);
        typeAliasRegistry.registerAlias("WEAK", WeakCache.class);

        typeAliasRegistry.registerAlias("DB_VENDOR", VendorDatabaseIdProvider.class);

        typeAliasRegistry.registerAlias("XML", XMLLanguageDriver.class);
        typeAliasRegistry.registerAlias("RAW", RawLanguageDriver.class);

        typeAliasRegistry.registerAlias("SLF4J", Slf4jImpl.class);
        typeAliasRegistry.registerAlias("COMMONS_LOGGING", JakartaCommonsLoggingImpl.class);
        typeAliasRegistry.registerAlias("LOG4J", Log4jImpl.class);
        typeAliasRegistry.registerAlias("LOG4J2", Log4j2Impl.class);
        typeAliasRegistry.registerAlias("JDK_LOGGING", Jdk14LoggingImpl.class);
        typeAliasRegistry.registerAlias("STDOUT_LOGGING", StdOutImpl.class);
        typeAliasRegistry.registerAlias("NO_LOGGING", NoLoggingImpl.class);

        typeAliasRegistry.registerAlias("CGLIB", CglibProxyFactory.class);
        typeAliasRegistry.registerAlias("JAVASSIST", JavassistProxyFactory.class);

        languageRegistry.setDefaultDriverClass(XMLLanguageDriver.class);
        languageRegistry.register(RawLanguageDriver.class);
    }
```

看看上面那个方法的前两行，就是定义的事务处理类别名！

然后看看，代码如何对`environment` 节点的`transactionManager` 子节点是如何解析的

```java
//XMLConfigBuilder 类  
private TransactionFactory transactionManagerElement(XNode context) throws Exception {
    if (context != null) {
      //获取事务配置节点的 type 属性
      String type = context.getStringAttribute("type");
      Properties props = context.getChildrenAsProperties();
      TransactionFactory factory = (TransactionFactory) resolveClass(type).newInstance();
      factory.setProperties(props);
      return factory;
    }
    throw new BuilderException("Environment declaration requires a TransactionFactory.");
  }
```

## 一级缓存

一级缓存就是我们平时说的基于`SqlSession`的缓存，但是通过前面的学习我们知道，SqlSession内部是委托给

`Executor`去做的，

###存在哪儿

那么`Executor` 是如何存我们的缓存数据的呢？

```java
//BaseExecutor 类  
private <E> List<E> queryFromDatabase(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
    List<E> list;
    //先向缓存中放入占位符？？？
    localCache.putObject(key, EXECUTION_PLACEHOLDER);
    try {
      list = doQuery(ms, parameter, rowBounds, resultHandler, boundSql);
    } finally {
      //最后删除占位符
      localCache.removeObject(key);
    }
    //加入缓存
    localCache.putObject(key, list);
    //如果是存储过程，OUT参数也加入缓存
    if (ms.getStatementType() == StatementType.CALLABLE) {
      localOutputParameterCache.putObject(key, parameter);
    }
    return list;
  }
```

通过上面的代码我们发现是`BaseExecutor` 的`localCache` ，来存储缓存的，看看这个属性的定义：

```java
  //本地缓存
  protected PerpetualCache localCache;
```

类`PerpetualCache` 内部也是使用Map 来存储缓存，

### Key

结合前面的学习我们知道这儿的可以是 `CacheKey` 

它是通过statementId,params,rowBounds，BoundSql来构建一个key值

```java
//CachingExecutor 类
@Override
public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException {
    BoundSql boundSql = ms.getBoundSql(parameterObject);
	//query时传入一个cachekey参数
    CacheKey key = createCacheKey(ms, parameterObject, rowBounds, boundSql);
    return query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
  }
```

看看`createCacheKey` 方法：

```java

```

### 生命周期

1. MyBatis在开启一个数据库会话时，会创建一个新的SqlSession对象，SqlSession对象中会有一个新的Executor对象，

Executor对象中持有一个新的PerpetualCache对象；当会话结束时，SqlSession对象及其内部的Executor对象还有PerpetualCache对象也一并释放掉。

2. 如果SqlSession调用了close()方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用。

3. 如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用。

   ```java
   // SqlSession 类
   //核心clearCache
     @Override
     public void clearCache() {
       //转而用执行器来clearLocalCache
       executor.clearLocalCache();
     }
   ```

   ​

4. 4.SqlSession中执行了任何一个update操作(update()、delete()、insert()) ，都会清空PerpetualCache对象的数据，但是该对象可以继续使用。

   ```java
   //BaseExecutor  
   @Override
     public int update(MappedStatement ms, Object parameter) throws SQLException {
       ErrorContext.instance().resource(ms.getResource()).activity("executing an update").object(ms.getId());
       if (closed) {
         throw new ExecutorException("Executor was closed.");
       }
       //先清局部缓存，再更新，如何更新交由子类，模板方法模式
       clearLocalCache();
       return doUpdate(ms, parameter);
     }
   ```

   ​

#类解释

## BaseStatementHandler

### 类结构介绍

此类实现了`StatementHandler`,我们都知道这个接口很重要，

我们来看看`BaseStatementHandler`,这个类中一些重要的方法和属性

```java
//保存着 ResultSetHandler 实例，在query后直接用来封装结果
protected final ResultSetHandler resultSetHandler;
```



## MappedStatement

一个namespace + id，在mybatis中用一个 MappedStatement 对象来存储，如“activity.insert”

可以通过如下的方式获取：

```
MappedStatement ms = configuration.getMappedStatement(statement);
```

## DefaultParameterHandler

顾明思意：这个类实现了`ParameterHandler`,是默认的参数赋值的实现，而且在mybatis源码中，此接口的唯一实现也就是`DefaultParameterHandler`了

### 重要方法

#### setParameters

同理这个方法一看就是用来预编译参数赋值，话不多说直接上代码：

```java
  public void setParameters(PreparedStatement ps) throws SQLException {
    ErrorContext.instance().activity("setting parameters").object(mappedStatement.getParameterMap().getId());
    List<ParameterMapping> parameterMappings = boundSql.getParameterMappings();
    if (parameterMappings != null) {
      for (int i = 0; i < parameterMappings.size(); i++) {
        ParameterMapping parameterMapping = parameterMappings.get(i);
        if (parameterMapping.getMode() != ParameterMode.OUT) {
          Object value;
          String propertyName = parameterMapping.getProperty();
          if (boundSql.hasAdditionalParameter(propertyName)) { // issue #448 ask first for additional params
            value = boundSql.getAdditionalParameter(propertyName);
          } else if (parameterObject == null) {
            value = null;
          } else if (typeHandlerRegistry.hasTypeHandler(parameterObject.getClass())) {
            value = parameterObject;
          } else {
            MetaObject metaObject = configuration.newMetaObject(parameterObject);
            value = metaObject.getValue(propertyName);
          }
          TypeHandler typeHandler = parameterMapping.getTypeHandler();
          JdbcType jdbcType = parameterMapping.getJdbcType();
          if (value == null && jdbcType == null) jdbcType = configuration.getJdbcTypeForNull();
          typeHandler.setParameter(ps, i + 1, value, jdbcType);
        }
      }
    }
  }
```



## DefaultResultSetHandler

getRowValue：看看能不能返回已经设置好值的对象

applyPropertyMappings：会向对象的属性赋值

```JAVA
 //stmt 就是java提供的sql API 的 statement
public List<Object> handleResultSets(Statement stmt) throws SQLException {
    //这个值来存放封装后的结果
    final List<Object> multipleResults = new ArrayList<Object>();

    int resultSetCount = 0;
    ResultSetWrapper rsw = getFirstResultSet(stmt);

    List<ResultMap> resultMaps = mappedStatement.getResultMaps();
    int resultMapCount = resultMaps.size();
    validateResultMapsCount(rsw, resultMapCount);
    while (rsw != null && resultMapCount > resultSetCount) {
      ResultMap resultMap = resultMaps.get(resultSetCount);
      //这一步里面会向 multipleResults 里面填充对象
      handleResultSet(rsw, resultMap, multipleResults, null);
      rsw = getNextResultSet(stmt);
      cleanUpAfterHandlingResultSet();
      resultSetCount++;
    }

    String[] resultSets = mappedStatement.getResulSets();
    if (resultSets != null) {
      while (rsw != null && resultSetCount < resultSets.length) {
        ResultMapping parentMapping = nextResultMaps.get(resultSets[resultSetCount]);
        if (parentMapping != null) {
          String nestedResultMapId = parentMapping.getNestedResultMapId();
          ResultMap resultMap = configuration.getResultMap(nestedResultMapId);
          handleResultSet(rsw, resultMap, null, parentMapping);
        }
        rsw = getNextResultSet(stmt);
        cleanUpAfterHandlingResultSet();
        resultSetCount++;
      }
    }

    return collapseSingleResultList(multipleResults);
  }
```



## ResultSetHandler

处理结果集的包装

## DefaultResultHandler：

用来赞暂存查询的已经封装好的结果：

```java
public class DefaultResultHandler implements ResultHandler {

  private final List<Object> list;

  public DefaultResultHandler() {
    list = new ArrayList<Object>();
  }

  @SuppressWarnings("unchecked")
  public DefaultResultHandler(ObjectFactory objectFactory) {
    list = objectFactory.create(List.class);
  }
  
  //当一个对象已经赋值完成的时候，就调用这个方法一个个的存入对象
  public void handleResult(ResultContext context) {
    list.add(context.getResultObject());
  }
  //有个地方会调用这儿来或者结果
  public List<Object> getResultList() {
    return list;
  }

}
```



## PreparedStatementHandler

###类结构介绍

此类继承于`BaseStatementHandler`，先来看看这个类



是`StatementHandler`接口的实现，这类型的接口对应着Java sql api 的Statement 有一定联系

###重要方法

####query(很重要)

其实就是用来执行并且封装结果：

```java
//这儿提一嘴，JDK原生代码和mysql驱动中都有一个 PreparedStatement 类，但是mysql驱动中的实现了jdk中的
//PreparedStatement 接口
public <E> List<E> query(Statement statement, ResultHandler resultHandler) throws SQLException {
    //就是Mysql驱动 原生的 预编译api：PreparedStatement，大家都很属性
    PreparedStatement ps = (PreparedStatement) statement;
    //这儿其实就是执行查询，并且得到结果集
    ps.execute();
    //使用ResultSetHandler ，开始包装结果集
    return resultSetHandler.<E> handleResultSets(ps);
  }
```

## RowBounds

这个类用来分页用，看看其中的属性就明白了

只截了重要的部分，

```java
  //offset,limit就等于一般分页的start,limit,
  private int offset;
  private int limit;

  //默认是一页Integer.MAX_VALUE条
  public RowBounds() {
    this.offset = NO_ROW_OFFSET;
    this.limit = NO_ROW_LIMIT;
  }
```

说明：默认，一页是取0-最大值，其实就是能查出来的全部的数据



# 面试题

##1.#{}和${}的区别是什么？

网上的答案是：#{}是预编译处理，${}是字符串替换。mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；mybatis在处理​${}时，就是把${}替换成变量的值。使用#{}可以有效的防止SQL注入，提高系统安全性。

对于这个题目我感觉要抓住两点：
（1）$符号一般用来当作占位符，常使用Linux脚本的人应该对此有更深的体会吧。既然是占位符，当然就是被用来替换的。知道了这点就能很容易区分$和#，从而不容易记错了。
（2）预编译的机制。预编译是提前对SQL语句进行预编译，而其后注入的参数将不会再进行SQL编译。我们知道，SQL注入是发生在编译的过程中，因为恶意注入了某些特殊字符，最后被编译成了恶意的执行操作。而预编译机制则可以很好的防止SQL注入。

最后想说的是，对于mybatis以及sql而言，每一个考点背后都是有一个深刻的思想存在的，应该好好的体会。这样才能真正的做到技术提升，成为技术大牛。

#### 1、#{}和${}的区别是什么？

注：这道题是面试官面试我同事的。

答：${}是Properties文件中的变量占位符，它可以用于标签属性值和sql内部，属于静态文本替换，比如${driver}会被静态替换为com.mysql.jdbc.Driver。#{}是sql的参数占位符，Mybatis会将sql中的#{}替换为?号，在sql执行前会使用PreparedStatement的参数设置方法，按序给sql的?号占位符设置参数值，比如ps.setInt(0, parameterValue)，#{item.name}的取值方式为使用反射从参数对象中获取item对象的name属性值，相当于param.getItem().getName()。

#### 2、Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？

注：这道题是京东面试官面试我时问的。

答：还有很多其他的标签，<resultMap>、<parameterMap>、<sql>、<include>、<selectKey>，加上动态sql的9个标签，trim|where|set|foreach|if|choose|when|otherwise|bind等，其中<sql>为sql片段标签，通过<include>标签引入sql片段，<selectKey>为不支持自增的主键生成策略标签。

#### 3、最佳实践中，通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

注：这道题也是京东面试官面试我时问的。

答：Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement，举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。在Mybatis中，每一个<select>、<insert>、<update>、<delete>标签，都会被解析为一个MappedStatement对象。

Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。

Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回。

#### 4、Mybatis是如何进行分页的？分页插件的原理是什么？

注：我出的。

答：Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

举例：select * from student，拦截sql后重写为：select t.* from （select * from student）t limit 0，10

#### 5、简述Mybatis的插件运行原理，以及如何编写一个插件。

注：我出的。

答：Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。

实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。

#### 6、Mybatis执行批量插入，能返回数据库主键列表吗？

注：我出的。

答：能，JDBC都能，Mybatis当然也能。

#### 7、Mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？

注：我出的。

答：Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能，Mybatis提供了9种动态sql标签trim|where|set|foreach|if|choose|when|otherwise|bind。

其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

#### 8、Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

注：我出的。

答：第一种是使用<resultMap>标签，逐一定义列名和对象属性名之间的映射关系。第二种是使用sql列的别名功能，将列别名书写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成T_NAME AS NaMe，Mybatis一样可以正常工作。

有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

#### 9、Mybatis能执行一对一、一对多的关联查询吗？都有哪些实现方式，以及它们之间的区别。

注：我出的。

答：能，Mybatis不仅可以执行一对一、一对多的关联查询，还可以执行多对一，多对多的关联查询，多对一查询，其实就是一对一查询，只需要把selectOne()修改为selectList()即可；多对多查询，其实就是一对多查询，只需要把selectOne()修改为selectList()即可。

关联对象查询，有两种实现方式，一种是单独发送一个sql去查询关联对象，赋给主对象，然后返回主对象。另一种是使用嵌套查询，嵌套查询的含义为使用join查询，一部分列是A对象的属性值，另外一部分列是关联对象B的属性值，好处是只发一个sql查询，就可以把主对象和其关联对象查出来。

那么问题来了，join查询出来100条记录，如何确定主对象是5个，而不是100个？其去重复的原理是<resultMap>标签内的<id>子标签，指定了唯一确定一条记录的id列，Mybatis根据<id>列值来完成100条记录的去重复功能，<id>可以有多个，代表了联合主键的语意。

同样主对象的关联对象，也是根据这个原理去重复的，尽管一般情况下，只有主对象会有重复记录，关联对象一般不会重复。

举例：下面join查询出来6条记录，一、二列是Teacher对象列，第三列为Student对象列，Mybatis去重复处理后，结果为1个老师6个学生，而不是6个老师6个学生。

​       t_id    t_name           s_id

|          1 | teacher      |      38 |
|          1 | teacher      |      39 |
|          1 | teacher      |      40 |
|          1 | teacher      |      41 |
|          1 | teacher      |      42 |
|          1 | teacher      |      43 |

#### 10、Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

注：我出的。

答：Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

#### 11、Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

注：我出的。

答：不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复；毕竟namespace不是必须的，只是最佳实践而已。

原因就是namespace+id是作为Map<String, MappedStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

#### 12、Mybatis中如何执行批处理？

注：我出的。

答：使用BatchExecutor完成批处理。

#### 13、Mybatis都有哪些Executor执行器？它们之间的区别是什么？

注：我出的

答：Mybatis有三种基本的Executor执行器，**SimpleExecutor、ReuseExecutor、BatchExecutor。**

**SimpleExecutor：**每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

**ReuseExecutor：**执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。

**BatchExecutor：**执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。

#### 14、Mybatis中如何指定使用哪一种Executor执行器？

注：我出的

答：在Mybatis配置文件中，可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数。

#### 15、Mybatis是否可以映射Enum枚举类？

注：我出的

答：Mybatis可以映射枚举类，不单可以映射枚举类，Mybatis可以映射任何对象到表的一列上。映射方式为自定义一个TypeHandler，实现TypeHandler的setParameter()和getResult()接口方法。TypeHandler有两个作用，一是完成从javaType至jdbcType的转换，二是完成jdbcType至javaType的转换，体现为setParameter()和getResult()两个方法，分别代表设置sql问号占位符参数和获取列查询结果。

#### 16、Mybatis映射文件中，如果A标签通过include引用了B标签的内容，请问，B标签能否定义在A标签的后面，还是说必须定义在A标签的前面？

注：我出的

答：虽然Mybatis解析Xml映射文件是按照顺序解析的，但是，被引用的B标签依然可以定义在任何地方，Mybatis都可以正确识别。

原理是，Mybatis解析A标签，发现A标签引用了B标签，但是B标签尚未解析到，尚不存在，此时，Mybatis会将A标签标记为未解析状态，然后继续解析余下的标签，包含B标签，待所有标签解析完毕，Mybatis会重新解析那些被标记为未解析的标签，此时再解析A标签时，B标签已经存在，A标签也就可以正常解析完成了。

#### 17、简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？

注：我出的

答：Mybatis将所有Xml配置信息都封装到All-In-One重量级对象Configuration内部。在Xml映射文件中，<parameterMap>标签会被解析为ParameterMap对象，其每个子元素会被解析为ParameterMapping对象。<resultMap>标签会被解析为ResultMap对象，其每个子元素会被解析为ResultMapping对象。每一个<select>、<insert>、<update>、<delete>标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

#### 18、为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？

注：我出的

答：Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

面试题看似都很简单，但是想要能正确回答上来，必定是研究过源码且深入的人，而不是仅会使用的人或者用的很熟的人，以上所有面试题及其答案所涉及的内容，在我的Mybatis系列博客中都有详细讲解和原理分析。



# Mybatis插件

## 是什么

是Mybatis提供的用来拓展我们功能的一种机制

## 自定义Mybatis插件

Mybatis提供了一个插件接口`org.apache.ibatis.plugin.Interceptor`，如果我们要自定义插件，就要定义一个实现了此接口的类。



# 总结

## 个人觉得Mybatis的源码写得不是很好？

第一很多的方法的一些参数，实际上没有使用，但是还是加上了，给阅读者和使用者带来视觉上的迷惑



