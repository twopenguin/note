# Servlet

## Servlet 结构

![servlet1](http://ohfk1r827.bkt.clouddn.com/servlet1.png)

### 1、Servlet 接口

Servlet 该接口定义了5个方法。

- init()，初始化 servlet 对象，完成一些初始化工作。它是由 servlet 容器控制的，该方法只能被调用一次
- service()，接受客户端请求对象，执行业务操作，利用响应对象响应客户端请求。
- destroy()，当容器监测到一个servlet从服务中被移除时，容器调用该方法，释放资源，该方法只能被调用一次。
- getServletConfig()，ServletConfig 是容器向 servlet 传递参数的载体。
- getServletInfo()，获取 servlet 相关信息。

Servlet 的生命周期：

> 1，初始化阶段  调用 init() 方法
>
> 2，响应客户请求阶段　　调用 service() 方法
>
> 3，终止阶段　　调用 destroy() 方法

在 Servlet 接口中的五个方法中涉及的接口有三个：ServletConfig 、 ServletRequest、 ServletResponse

这里先讲讲 ServletRequest 和 ServletResponse。

1）ServletRequest  由 Servlet 容器来管理，当客户请求到来时，容器创建一个 ServletRequest 对象，封装请求数据，同时创建一个 ServletResponse 对象，封装响应数据。这两个对象将被容器作为 service（）方法的参数传递给 Servlet，Serlvet 利用 ServletRequest 对象获取客户端发来的请求数据，利用 ServletResponse 对象发送响应数据。

下面是 ServletRequest  中所有的方法，根据方法名大概就可以猜到这些方法到底是干啥用的。

![ServletRequest](http://ohfk1r827.bkt.clouddn.com/ServletRequest.png)

2）ServletResponse 发送响应数据

![ServletResponse](http://ohfk1r827.bkt.clouddn.com/ServletResponse.png)

### 2、ServletConfig

ServletConfig 是容器向 servlet 传递参数的载体。

ServletConfig的4个常用方法：

1）public String getInitParameter（String name）：返回指定名称的初始化参数值；

2）public Enumeration getInitParameterNames（）：返回一个包含所有初始化参数名的 Enumeration 对象；

3）public String getServletName()：返回在 DD 文件中`<servlet-name>`元素指定的 Servlet 名称；

4）public ServletContext getServletContext（）：返回该 Servlet 所在的上下文对象；

这里详细讲下 ServletContext ：

![ServletContext](http://ohfk1r827.bkt.clouddn.com/ServletContext.png)

Servlet 上下文对象（ServletContext）：每个Web应用程序在被启动时都会创建一个唯一的上下文对象，Servlet 可通过其获得 Web 应用程序的初始化参数或 Servlet 容器的版本等信息，也可被 Servlet 用来与其他 Servlet 共享数据。

1、获得 ServletContext 应用：

（1）、直接调用 getServletContext（）方法

ServletContext context = getServletContext（）;

（2）、使用 ServletConfig 应用，再调用它的 getServletContext（）方法

ServletContext context = getServletConfig.getServletContext();

2、获得应用程序的初始化参数：

（1）、public String getInitParameter（String name）：返回指定参数名的字符串参数值，没有则返回 null；

（2）、public Enumeration getInitParameterNames()：返回一个包含多有初始化参数名的 Enumeration 对象；

3、通过 ServletContext 对象获得资源

（1）、public URl getResource（String path）:返回由给定路径的资源的 URL 对象，以 “/” 开头，为相对路径，相对于Web 应用程序的文档根目录；

（2）、public InputStream getResourceAsStream（String path）：从资源上获得一个 InputStream 对象，等价于getResource（path）.oprenStream();

（3）、public String getRealPath(String path)：返回给定的虚拟路径的真实路径；

4、登陆日志：使用 log（）方法可以将指定的消息写到服务器的日志文件中

（1）、public void log（String msg）：参数 msg 为写入日志文件消息

（2）、public void log（String msg，Throwable throwable）：将 msg 指定的消息和异常的栈跟踪信息写入日志文件

5、使用 RequestDispatcher 实现请求转发

（1）、RequestDispatcher getRequestDiapatcher(String path)：必须以 “/“ 开头相对于应用程序根目录，而ServletRequest 可以传递一个相对路径

（2）、RequestDipatcher getNamedDiapatcher（String name）：参数 name 为一个命名的 Servlet 对象

6、使用 ServletContext 对象存储数据

（1）、public void serAttribute（String name，Object object）：将给定名称的属性值对象绑定到上下文对象上；

（2）、public Object getAttribute（String name）：返回绑定到上下文对象的给定名称的属性值；

（3）、public Enumeration getAttributeNames()：返回绑定到上下文对象上的所有属性名的 Enumeration 对象；

（4）、public void removeAttribute（String name）：删除绑定到上下文对象指定名称的属性；

ServletRequest 共享的对象仅在请求的生存周期中可以被访问；

HttpSession 共享的对象仅在会话的生存周期中可以被访问；

ServletContext 共享的对象在整个 Web 应用程序启动的生存周期中可以被访问；

7、检索 Servlet 容器的信息

（1）、public String getServletInfo()：返回 Servlet 所运行容器的名称和版本；

（2）、public int getMajorVersion（）：返回容器所支持的 Servlet API 的主版本号；

（3）、public int  getMinorVersion（）：返回容器所支持的 Servlet API 的次版本号；

（4）、public String getServletContext（）：返回 ServletContext 对应的 web 应用程序名称 `<display-name>`元素定义的名称；

### 3、GenericServlet 抽象类

GenericServlet  定义了一个通用的，不依赖具体协议的 Servlet，它实现了 Servlet 接口和 ServletConfig 接口，它的方法在文章的第一张图就给出了。

### 4、HttpServlet 抽象类

#### 4.1、HTTP 请求方式

![http-request-method](http://ohfk1r827.bkt.clouddn.com/http-request-method.jpg)

1. GET : 获取由请求 URL 标识的资源
2. POST : 向 Web 服务器发送无限制长度的数据
3. PUT : 存储一个资源到请求的 URL
4. DELETE : 删除由 URL 标识的资源
5. HEAD : 返回 URL 标识的头信息
6. OPTIONS : 返回服务器支持的 HTTP 方法
7. TRACE : 返回 TRACE 请求附带的头字段

#### 4.2、对应的服务方法：

1. doGet() : 调用服务器的资源, 并将其作为响应返回给客户端.  doGet() 调用在 URL 里显示正在传送给 Servlet 的数据,这在系统的安全方面可能带来一些问题, 比如说, 用户登录时,  表单里的用户名和密码需要发送到服务器端, doGet() 调用会在浏览器的 URL 里显示用户名和密码.
2. doPost() : 它用于把客户端的数据传给服务端, 使用它可以以隐藏方式给服务器端发送数据. Post 适合发送大量数据.
3. doPut() : 调用和 doPost() 相似, 并且它允许客户端把真正的文件存放在服务器上, 而不仅仅是传送数据.
4. doDelete() : 它允许客户端删除服务器端的文件或者 Web 页面．它的使用非常少．
5. doHead() : 它用于处理客户端的 Head 调用,并且返回一个 response. 当客户端只需要响应的 Header 时,它就发出一个Header 请求.这种情况下客户端往往关心响应的长度和响应的 MIME 类型.
6. doOptions(): 它用于处理客户端的 Options 调用,通过这个调用, 客户端可以获得此 Servlet 支持的方法.如果 Servlet 覆盖了 doPost() 方法, 那么将返回: Allow: POST, TRACE, OPTIONS, HEAD
7. doTrace：处理 TRACE 请求

#### 4.3、service(ServletRequest req, ServletResponse res) 方法详解

```java
public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException
    {
        HttpServletRequest  request;
        HttpServletResponse response;
        // 如果传入的 HTTP 请求和 HTTP 响应不是 HTTP 的领域模型，则抛出 Servlet 异常，这个异常会被 Servlet 容器所处理
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }
		// 既然是 HTTP 协议绑定的 Serlvet, 强制转换到 HTTP 的领域模型
        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;
		// 如果传入的请求和响应是预期的 HTTP 请求和 HTTP 响应，则调用 HttpServlet 的 service() 方法。
        service(request, response);
    }

```

#### 4.4、service(HttpServletRequest req, HttpServletResponse resp)方法详解

```java
protected void service(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
    // 从 HTTP 请求中取得这次请求所使用的 HTTT 方法
        String method = req.getMethod();
		// 如果这次请求使用 GET 方法
        if (method.equals(METHOD_GET)) {
        	// 取得这个 Servlet 的最后修改的时间
            long lastModified = getLastModified(req);
            if (lastModified == -1) {
                // servlet doesn't support if-modified-since, no reason
                // to go through further expensive logic
                //-1 代表这个 Servlet 不支持最后修改操作，直接调用 doGet() 进行处理 HTTP GET 请求
                doGet(req, resp);
            } else {
            	// 如果这个 Servlet 支持最后修改操作，取得请求头中包含的请求的最后修改时间
                long ifModifiedSince = req.getDateHeader(HEADER_IFMODSINCE);
                if (ifModifiedSince < lastModified) {
                    // If the servlet mod time is later, call doGet()
                    // Round down to the nearest second for a proper compare
                    // A ifModifiedSince of -1 will always be less
                    // 如果请求头中包含的修改时间早于这个 Servlet 的最后修改时间，说明这个 Servlet 自从客户上一次 HTTP 请求已经被修改了 , 设置最新修改时间到响应头中
                    maybeSetLastModified(resp, lastModified);
                    // 调用 doGet 进行进行处理 HTTP GET 请求
                    doGet(req, resp);
                } else {
                // 如果请求头中包含修改时间晚于这个 Servlet 的最后修改时间，说明这个 Servlet 自从请求的最后修改时间后没有更改过，这种情况下，仅仅返回一个 HTTP 响应状态 SC_NOT_MODIFIED
                    resp.setStatus(HttpServletResponse.SC_NOT_MODIFIED);
                }
            }

        } else if (method.equals(METHOD_HEAD)) {
        // 如果这次请求使用 HEAD 方法
         // 如果这个 Servlet 支持最后修改操作，则设置这个 Servlet 的最后修改时间到响应头中
            long lastModified = getLastModified(req);
            maybeSetLastModified(resp, lastModified);
            // 和对 HTTP GET 方法处理不同的是，无论请求头中的修改时间是不是早于这个 Sevlet 的最后修改时间，都会发 HEAD 响应给客户，因为 HTTP HEAD 响应是用来查询 Servlet 头信息的操作
            doHead(req, resp);

        } else if (method.equals(METHOD_POST)) {
        // 如果这次请求使用 POST 方法
            doPost(req, resp);

        } else if (method.equals(METHOD_PUT)) {
        // 如果这次请求使用 PUT 方法
            doPut(req, resp);

        } else if (method.equals(METHOD_DELETE)) {
        // 如果这次请求使用 DELETE 方法
            doDelete(req, resp);

        } else if (method.equals(METHOD_OPTIONS)) {
        // 如果这次请求使用 OPTIONS 方法
            doOptions(req,resp);

        } else if (method.equals(METHOD_TRACE)) {
        // 如果这次请求使用 TRACE 方法
            doTrace(req,resp);

        } else {
            // Note that this means NO servlet supports whatever
            // method was requested, anywhere on this server.
			// 如果这次请求是其他未知方法，返回错误代码 SC_NOT_IMPLEMENTED 给 HTTP 响应，并且显示一个错误消息，说明这个操作是没有实现的
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[1];
            errArgs[0] = method;
            errMsg = MessageFormat.format(errMsg, errArgs);

            resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
        }
    }

```

### 5、Servlet 的多线程问题

1、当涉及到 Servlet 需要共享资源是，需保证 Servlet 是线程安全的

2、注意事项：

（1）、用方法的局部变量保持请求中的专有数据；

（2）、只用 Servlet 的成员变量来存放那些不会改变的数据；

（3）、对可能被请求修改的成员变量同步（用 Synchronized 关键字修饰）；

（4）、如果 Servlet 访问外部资源，那么需要同步访问这些资源；

3、实现 SingleThreadModel 接口的 Servlet 在被多个客户请求时一个时刻只能有一个线程运行，不推荐使用。

4、如果必须在 servlet 使用同步代码，应尽量在最小的范围上（代码块）进行同步，同步代码越少，Servlet 执行才能越好，避免对 doGet() 或 doPost() 方法同步。

## 总结

全文首先通过一张 Servlet 中的核心 Servlet 类图关系，了解了几种 Servlet 之间的关系及其内部方法。然后在分别介绍这几种 Servlet，通过分析部分重要方法的源码来了解，还介绍了 Servlet 中多线程的问题的解决方法。