# JDK 源码

## 集合

### Map

#### LinkedHashMap(1.8)

##### 为啥是有序的

因为`LinkedHashMap`的Entry节点是使用双向链表来维持插入的顺序

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
  Entry<K,V> before, after;
  Entry(int hash, K key, V value, Node<K,V> next) {
    super(hash, key, value, next);
  }
}
```





# Java URL 协议扩展

## 关键类

* URL
* URLConnection
* URLStreamHandler
* URLStreamHandlerFactory



## 流程

1，先看看JDK中的`Launcher` 类的如下定义：

```java
private static URLStreamHandlerFactory factory = new Launcher.Factory();
```

Factory 是 Launcher 的一个内部类

```java
private static class Factory implements URLStreamHandlerFactory {
	private static String PREFIX = "sun.net.www.protocol";

	private Factory() {
	}

	public URLStreamHandler createURLStreamHandler(String var1) {
		String var2 = PREFIX + "." + var1 + ".Handler";

		try {
			Class var3 = Class.forName(var2);
			return (URLStreamHandler)var3.newInstance();
		} catch (ReflectiveOperationException var4) {
			throw new InternalError("could not load " + var1 + "system protocol handler", var4);
		}
	}
}
```

2.我们使用一个字符串来创建URL对象的时候

```java
URL url = new URL("classpath:/META-INF/license.txt");
```

进入URL 看源码,看参数最多的那个构造器

```java
if (handler == null &&
	(handler = getURLStreamHandler(protocol)) == null) {
	throw new MalformedURLException("unknown protocol: "+protocol);
}
```

进入`getURLStreamHandler`   方法：

```java
static URLStreamHandler getURLStreamHandler(String protocol) {

	URLStreamHandler handler = handlers.get(protocol);
	if (handler == null) {

		boolean checkedWithFactory = false;

		// Use the factory (if any)
		if (factory != null) {
			handler = factory.createURLStreamHandler(protocol);
			checkedWithFactory = true;
		}

		// Try java protocol handler
		if (handler == null) {
			String packagePrefixList = null;

			packagePrefixList
				= java.security.AccessController.doPrivileged(
				new sun.security.action.GetPropertyAction(
					protocolPathProp,""));
			if (packagePrefixList != "") {
				packagePrefixList += "|";
			}

			// REMIND: decide whether to allow the "null" class prefix
			// or not.
			packagePrefixList += "sun.net.www.protocol";

			StringTokenizer packagePrefixIter =
				new StringTokenizer(packagePrefixList, "|");

			while (handler == null &&
				   packagePrefixIter.hasMoreTokens()) {

				String packagePrefix =
				  packagePrefixIter.nextToken().trim();
				try {
					String clsName = packagePrefix + "." + protocol +
					  ".Handler";
					Class<?> cls = null;
					try {
						cls = Class.forName(clsName);
					} catch (ClassNotFoundException e) {
						ClassLoader cl = ClassLoader.getSystemClassLoader();
						if (cl != null) {
							cls = cl.loadClass(clsName);
						}
					}
					if (cls != null) {
						handler  =
						  (URLStreamHandler)cls.newInstance();
					}
				} catch (Exception e) {
					// any number of exceptions can get thrown here
				}
			}
		}

		synchronized (streamHandlerLock) {

			URLStreamHandler handler2 = null;

			// Check again with hashtable just in case another
			// thread created a handler since we last checked
			handler2 = handlers.get(protocol);

			if (handler2 != null) {
				return handler2;
			}

			// Check with factory if another thread set a
			// factory since our last check
			if (!checkedWithFactory && factory != null) {
				handler2 = factory.createURLStreamHandler(protocol);
			}

			if (handler2 != null) {
				// The handler from the factory must be given more
				// importance. Discard the default handler that
				// this thread created.
				handler = handler2;
			}

			// Insert this handler into the hashtable
			if (handler != null) {
				handlers.put(protocol, handler);
			}

		}
	}

	return handler;

}
```

这个方法会使用第一步中的factory，创建此协议对应打得Handler，下面的代码是为了避免懒加载多线程问题！

```java
synchronized (streamHandlerLock) {
```

##自定义协议

### 创建包

```
sun.net.www.protocol.classpath
```

### 包下创类

此类从上面可以看出叫Hander，实现接口`URLStreamHandler`

```java
public class Handler implements URLStreamHandler {
    @Override
    protected URLConnection openConnection(URL u) throws IOException {
        return null;
    }
}
```

创建一个类，继承`URLConnection` 

```java
public class ClassPathURLConnection extends URLConnection {

    public ClassPathURLConnection(URL url) {
        super(url);
    }

    @Override
    public void connect() throws IOException {

    }
}
```



# JMX

JMX的全称为`Java Management Extensions`. 顾名思义，是管理Java的一种扩展。这种机制可以方便的管理、监控正在运行中的Java程序。常用于管理线程，内存，日志Level，服务重启，系统环境等。



