## Servlet 学习笔记

### 1 Servlet 简介

#### 1.1 Servlet 是什么？

​	Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。

​	使用 Servlet，您可以收集来自网页表单的用户输入，呈现来自数据库或者其他源的记录，还可以动态创建网页。

​	Java Servlet 通常情况下与使用 CGI（Common Gateway Interface，公共网关接口）实现的程序可以达到异曲同工的效果。但是相比于 CGI，Servlet 有以下几点优势：

- 性能明显更好。
- Servlet 在 Web 服务器的地址空间内执行。这样它就没有必要再创建一个单独的进程来处理每个客户端请求。
- Servlet 是独立于平台的，因为它们是用 Java 编写的。
- 服务器上的 Java 安全管理器执行了一系列限制，以保护服务器计算机上的资源。因此，Servlet 是可信的。
- Java 类库的全部功能对 Servlet 来说都是可用的。它可以通过 sockets 和 RMI 机制与 applets、数据库或其他软件进行交互。

#### 1.2 Servlet 架构

![icon](/Users/lightwingng/Desktop/img/ServletArchitecture.jpg)

#### 1.3 Servlet 任务

- **读取客户端（浏览器）发送的显式的数据**。这包括网页上的 HTML 表单，或者也可以是来自 applet 或自定义的 HTTP 客户端程序的表单。
- **读取客户端（浏览器）发送的隐式的 HTTP 请求数据**。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
- **处理数据并生成结果**。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应。
- **发送显式的数据（即文档）到客户端（浏览器）**。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
- **发送隐式的 HTTP 响应到客户端（浏览器）**。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

#### 1.4 Servlet 包

​	Java Servlet 是运行在带有支持 Java Servlet 规范的解释器的 web 服务器上的 Java 类。

​	Servlet 可以使用 `javax.servlet` 和 `javax.servlet.http` 包创建，它是 Java 企业版的标准组成部分，Java 企业版是支持大型开发项目的 Java 类库的扩展版本。

### 2 Servlet 环境设置

#### 2.1 设置 Java 开发工具包 IntelliJ IDEA(macOS X 10.5+)

To add an element to a Web module:

1. Open the Web tool window by choosing `View | Tool Windows | Web`. The tool window displays all the modules with Web facets.

2. Right-click the desired `Web` module node and choose `New | Servlet/Filter/Listener` on the context menu.

3. In the `New Servlet/Filter/Listener` dialog box that opens, type the name of the element.

4. Specify the package where the element will be created.

5. Specify the class that implements the element.

6. Click `OK`. A new class is created and opened in the editor.

7. Proceed as described in the relevant element-specific topic.

   1]: https://www.jetbrains.com/help/idea/creating-and-deleting-web-application-elements-general-steps.html	"Creating and Deleting Web Application Elements - General Steps"

#### 2.2 设置 Web 服务器：Tomcat

​	启动

```powershell
$ cd /bin/
$ chmod +x *.sh
$ ./startup.sh
```

​	关闭

```powershell
$ ./shutdown.sh
```

#### 2.3 设置 `CLASSPATH`

```powershell
setenv CATALINA=/usr/local/apache-tomcat-5.5.29
setenv CLASSPATH $CATALINA/common/lib/servlet-api.jar:$CLASSPATH
```

### 3 Servlet 生命周期

​	Servlet 生命周期可被定义为从创建直到毁灭的整个过程。以下是 Servlet 遵循的过程：

1. Servlet 通过调用 `init ()` 方法进行初始化；
2. Servlet 调用 `service()` 方法来处理客户端的请求；
3. Servlet 通过调用 `destroy()` 方法终止（结束）；
4. 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

#### 3.1 `init()` 方法

​	`init` 方法被设计成只调用一次。

​	当用户调用一个 Servlet 时，就会创建一个 Servlet 实例，每一个用户请求都会产生一个新的线程，适当的时候移交给 `doGet` 或 `doPost` 方法。`init()` 方法简单地创建或加载一些数据，这些数据将被用于 Servlet 的整个生命周期。

​	init 方法的定义如下：

```java
public void init() throws ServletException {
	// 初始化代码...
}
```

#### 3.2 `service()` 方法

​	`service()` 方法是执行实际任务的主要方法。Servlet 容器（即 Web 服务器）调用 `service()` 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。

每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 `doGet`、`doPost`、`doPut`，`doDelete` 等方法。

```java
public void service(ServletRequest request, 
                    ServletResponse response) 
      throws ServletException, IOException {
}
```

#### 3.3 `doGet()` 方法

​	GET 请求来自于一个 URL 的正常请求，或者来自于一个未指定 METHOD 的 HTML 表单，它由 doGet() 方法处理。

```java
public void doGet(HttpServletRequest request,
                  HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
```

#### 3.4 `doPost()` 方法

​	POST 请求来自于一个特别指定了 METHOD 为 POST 的 HTML 表单，它由 doPost() 方法处理。

```java
public void doPost(HttpServletRequest request,
                   HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
```

#### 3.5 `destroy()` 方法

​	destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。`destroy()` 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。

​	在调用 `destroy()` 方法之后，servlet 对象被标记为垃圾回收。destroy 方法定义如下所示：

```java
public void destroy() {
    // 终止化代码...
}
```

#### 3.6 架构图

![icon](/Users/lightwingng/Desktop/img/Servlet-LifeCycle.jpg)

- 第一个到达服务器的 HTTP 请求被委派到 Servlet 容器。
- Servlet 容器在调用 `service()` 方法之前加载 Servlet。
- 然后 Servlet 容器处理由多个线程产生的多个请求，每个线程执行一个单一的 Servlet 实例的 `service()` 方法。

### 4 Servlet 实例

​	Servlet 是服务 HTTP 请求并实现 `javax.servlet.Servlet` 接口的 Java 类。Web 应用程序开发人员通常编写 Servlet 来扩展 `javax.servlet.http.HttpServlet`，并实现 Servlet 接口的抽象类专门用来处理 HTTP 请求。

#### 4.1 Hello World 示例代码

```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

// 扩展 HttpServlet 类
public class HelloWorld extends HttpServlet {
    private String message;

    public void init() throws ServletException {
        // 执行必需的初始化
        message = "Hello World";
    }

    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html");

        // 实际的逻辑是在这里
        PrintWriter out = response.getWriter();
        out.println("<h1>" + message + "</h1>");
    }

    public void destroy() {
        // 什么也不做
    }
}
```

#### 4.2 编译 Servlet

​	在 IntelliJ IDEA 中添加 Tomcat 的 Module，运行方可。

![icon](/Users/lightwingng/Desktop/img/ServletIDEA.png)

#### 4.3 Servlet 部署

​	默认情况下，Servlet 应用程序位于路径 `<Tomcat-installation-directory>/webapps/ROOT` 下，且类文件放在 `<Tomcat-installation-directory>/webapps/ROOT/WEB-INF/classes` 中。

​	如果您有一个完全合格的类名称 `**com.myorg.MyServlet**`，那么这个 Servlet 类必须位于 `WEB-INF/classes/com/myorg/MyServlet.class` 中。

​	现在，让我们把 `HelloWorld.class` 复制到 `<Tomcat-installation-directory>/webapps/ROOT/WEB-INF/classes` 中，并在位于 `<Tomcat-installation-directory>/webapps/ROOT/WEB-INF/` 的 `**web.xml**` 文件中创建以下条目：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>HelloWorld</servlet-name>
        <servlet-class>HelloWorld</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>HelloWorld</servlet-name>
        <url-pattern>/HelloWorld</url-pattern>
    </servlet-mapping>
</web-app>
```

### 5 Servlet 表单数据

#### 5.1 GET 方法

​	如果您要向服务器传递的是密码或其他的敏感信息，请不要使用 GET 方法。GET 方法有大小限制：请求字符串中最多只能有 1024 个字符。

#### 5.2 POST 方法

​	另一个向后台程序传递信息的比较可靠的方法是 `POST` 方法。

#### 5.3 使用 Servlet 读取表单数据

​	Servlet 处理表单数据，这些数据会根据不同的情况使用不同的方法自动解析：

`getParameter()`：您可以调用 `request.getParameter()` 方法来获取表单参数的值。

`getParameterValues()`：如果参数出现一次以上，则调用该方法，并返回多个值，例如复选框。

`getParameterNames()`：如果您想要得到当前请求中的所有参数的完整列表，则调用该方法。

#### 5.4 使用 URL 的 GET 方法实例

`HelloForm.java`

```java
import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


// Servlet implementation class HelloForm
@WebServlet("/HelloForm")
public class HelloForm extends HttpServlet {
    private static final long serialVersionUID = 1L;

    // @see HttpServlet#HttpServlet()
    public HelloForm() {
        super();
        // TODO Auto-generated constructor stub
    }

    // @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");

        PrintWriter out = response.getWriter();
        String title = "使用 GET 方法读取表单数据";
        // 处理中文
        String name = new String(request.getParameter("name").getBytes("ISO8859-1"), "UTF-8");
        String docType = "<!DOCTYPE html> \n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<ul>\n" +
                "  <li><b>站点名</b>："
                + name + "\n" +
                "  <li><b>网址</b>："
                + request.getParameter("url") + "\n" +
                "</ul>\n" +
                "</body></html>");
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

`web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
  <servlet>
    <servlet-name>HelloForm</servlet-name>
    <servlet-class>com.runoob.test.HelloForm</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>HelloForm</servlet-name>
    <url-pattern>/TomcatTest/HelloForm</url-pattern>
  </servlet-mapping>
</web-app>
```

#### 5.5 使用表单的 `GET` 方法实例

​	创建 `hello.html` 文件：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>菜鸟教程(runoob.com)</title>
</head>
<body>
<form action="HelloForm" method="GET">
    网址名：<input type="text" name="name">
    <br/>
    网址：<input type="text" name="url"/>
    <input type="submit" value="提交"/>
</form>
</body>
</html>
```

​	此时文档结构应如下：

```powershell
.
├── .idea
│   ├── artifacts
│   │   └── ServletDemo_war_exploded.xml
│   ├── misc.xml
│   ├── modules.xml
│   └── workspace.xml
├── ServletDemo.iml
├── out
│   ├── artifacts
│   │   └── ServletDemo_war_exploded
│   │       ├── WEB-INF
│   │       │   ├── classes
│   │       │   │   └── com
│   │       │   │       └── runoob
│   │       │   │           └── test
│   │       │   │               └── HelloForm.class
│   │       │   └── web.xml
│   │       ├── hello.html
│   │       └── index.jsp
│   └── production
│       └── ServletDemo
│           └── com
│               └── runoob
│                   └── test
│                       └── HelloForm.class
├── src
│   └── com
│       └── runoob
│           └── test
│               └── HelloForm.java
└── web
    ├── WEB-INF
    │   ├── classes
    │   │   └── com.myorg
    │   ├── lib
    │   └── web.xml
    ├── hello.html
    └── index.jsp
```

#### 5.6 使用表单的 POST 方法实例

在 HelloForm.java 中使用 GET 和 POST 的方法处理 Web 浏览器提交的输入

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class HelloForm
 */
@WebServlet("/HelloForm")
public class HelloForm extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public HelloForm() {
        super();
        // TODO Auto-generated constructor stub
    }

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");

        PrintWriter out = response.getWriter();
        String title = "使用 POST 方法读取表单数据";
        // 处理中文
        String name = new String(request.getParameter("name").getBytes("ISO8859-1"), "UTF-8");
        String docType = "<!DOCTYPE html> \n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<ul>\n" +
                "  <li><b>站点名</b>："
                + name + "\n" +
                "  <li><b>网址</b>："
                + request.getParameter("url") + "\n" +
                "</ul>\n" +
                "</body></html>");
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

​	将 `hello.html` 中的 `GET` 改为 `POST`

```html
<form action="HelloForm" method="GET">
```

#### 5.7 将复选框数据传递到 Servlet 程序

​	创建包含复选框的 HTML 文件 `checkbox.html` 如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>菜鸟教程(runoob.com)</title>
</head>
<body>
<form action="CheckBox" method="POST" target="_blank">
    <input type="checkbox" name="runoob" checked="checked"/> 菜鸟教程
    <input type="checkbox" name="google"/> Google
    <input type="checkbox" name="taobao" checked="checked"/> 淘宝
    <input type="submit" value="选择站点"/>
</form>
</body>
</html>
```

​	并添加 `CheckBox.java Servlet` 程序，处理 Web 浏览器提交的复选框输入：

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class CheckBox
 */
@WebServlet("/CheckBox")
public class CheckBox extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");

        PrintWriter out = response.getWriter();
        String title = "读取复选框数据";
        String docType = "<!DOCTYPE html> \n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<ul>\n" +
                "  <li><b>菜鸟按教程标识：</b>: "
                + request.getParameter("runoob") + "\n" +
                "  <li><b>Google 标识：</b>: "
                + request.getParameter("google") + "\n" +
                "  <li><b>淘宝标识：</b>: "
                + request.getParameter("taobao") + "\n" +
                "</ul>\n" +
                "</body></html>");
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

​	配置 `web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
    <servlet>
        <servlet-name>HelloForm</servlet-name>
        <servlet-class>com.runoob.test.HelloForm</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloForm</servlet-name>
        <url-pattern>/TomcatTest/HelloForm</url-pattern>
    </servlet-mapping>
</web-app>
```

​	此时文件结构应如下：

```powershell
.
├── .idea
│   ├── artifacts
│   │   └── ServletDemo_war_exploded.xml
│   ├── misc.xml
│   ├── modules.xml
│   └── workspace.xml
├── ServletDemo.iml
├── out
│   ├── artifacts
│   │   └── ServletDemo_war_exploded
│   │       ├── WEB-INF
│   │       │   ├── classes
│   │       │   │   └── com
│   │       │   │       └── runoob
│   │       │   │           └── test
│   │       │   │               ├── CheckBox.class
│   │       │   │               └── HelloForm.class
│   │       │   └── web.xml
│   │       ├── checkbox.html
│   │       ├── hello.html
│   │       └── index.jsp
│   └── production
│       └── ServletDemo
│           └── com
│               └── runoob
│                   └── test
│                       ├── CheckBox.class
│                       └── HelloForm.class
├── src
│   └── com
│       └── runoob
│           └── test
│               ├── CheckBox.java
│               └── HelloForm.java
└── web
    ├── WEB-INF
    │   ├── classes
    │   │   └── com.myorg
    │   ├── lib
    │   └── web.xml
    ├── checkbox.html
    ├── hello.html
    └── index.jsp
```

#### 5.8 读取所有的表单参数

`com/runoob/test/ReadParams.java`

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class ReadParams
 */
@WebServlet("/ReadParams")
public class ReadParams extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public ReadParams() {
        super();
        // TODO Auto-generated constructor stub
    }

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String title = "读取所有的表单数据";
        String docType =
                "<!doctype html public \"-//w3c//dtd html 4.0 " +
                        "transitional//en\">\n";
        out.println(docType +
                "<html>\n" +
                "<head><meta charset=\"utf-8\"><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<table width=\"100%\" border=\"1\" align=\"center\">\n" +
                "<tr bgcolor=\"#949494\">\n" +
                "<th>参数名称</th><th>参数值</th>\n" +
                "</tr>\n");

        Enumeration paramNames = request.getParameterNames();

        while (paramNames.hasMoreElements()) {
            String paramName = (String) paramNames.nextElement();
            out.print("<tr><td>" + paramName + "</td>\n");
            String[] paramValues =
                    request.getParameterValues(paramName);
            // 读取单个值的数据
            if (paramValues.length == 1) {
                String paramValue = paramValues[0];
                if (paramValue.length() == 0)
                    out.println("<td><i>没有值</i></td>");
                else
                    out.println("<td>" + paramValue + "</td>");
            } else {
                // 读取多个值的数据
                out.println("<td><ul>");
                for (int i = 0; i < paramValues.length; i++) {
                    out.println("<li>" + paramValues[i]);
                }
                out.println("</ul></td>");
            }
            out.print("</tr>");
        }
        out.println("\n</table>\n</body></html>");
    }

    /**
     * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // TODO Auto-generated method stub
        doGet(request, response);
    }
}
```

`test.html`

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>菜鸟教程(runoob.com)</title>
</head>
<body>
<form action="ReadParams" method="POST" target="_blank">
    <input type="checkbox" name="maths" checked="checked"/> 数学
    <input type="checkbox" name="physics"/> 物理
    <input type="checkbox" name="chemistry" checked="checked"/> 化学
    <input type="submit" value="选择学科"/>
</form>
</body>
</html>
```

### 6 Servlet 客户端 HTTP 请求

​	请求报文

| 头信息              | 描述                                                         |
| :------------------ | :----------------------------------------------------------- |
| Accept              | 这个头信息指定浏览器或其他客户端可以处理的 `MIME` 类型。值 `**image/png**` 或 **image/jpeg** 是最常见的两种可能值。 |
| Accept-Charset      | 这个头信息指定浏览器可以用来显示信息的字符集。例如 ISO-8859-1。 |
| Accept-Encoding     | 这个头信息指定浏览器知道如何处理的编码类型。值 **gzip** 或 **compress** 是最常见的两种可能值。 |
| Accept-Language     | 这个头信息指定客户端的首选语言，在这种情况下，Servlet 会产生多种语言的结果。例如，en、en-us、ru 等。 |
| Authorization       | 这个头信息用于客户端在访问受密码保护的网页时识别自己的身份。 |
| Connection          | 这个头信息指示客户端是否可以处理持久 HTTP 连接。持久连接允许客户端或其他浏览器通过单个请求来检索多个文件。值 **Keep-Alive** 意味着使用了持续连接。 |
| Content-Length      | 这个头信息只适用于 POST 请求，并给出 POST 数据的大小（以字节为单位）。 |
| Cookie              | 这个头信息把之前发送到浏览器的 cookies 返回到服务器。        |
| Host                | 这个头信息指定原始的 URL 中的主机和端口。                    |
| If-Modified-Since   | 这个头信息表示只有当页面在指定的日期后已更改时，客户端想要的页面。如果没有新的结果可以使用，服务器会发送一个 304 代码，表示 **Not Modified** 头信息。 |
| If-Unmodified-Since | 这个头信息是 If-Modified-Since 的对立面，它指定只有当文档早于指定日期时，操作才会成功。 |
| Referer             | 这个头信息指示所指向的 Web 页的 URL。例如，如果您在网页 1，点击一个链接到网页 2，当浏览器请求网页 2 时，网页 1 的 URL 就会包含在 Referer 头信息中。 |
| User-Agent          | 这个头信息识别发出请求的浏览器或其他客户端，并可以向不同类型的浏览器返回不同的内容。 |

#### 6.1 读取 HTTP 头的方法

​	下面的方法可用在 Servlet 程序中读取 HTTP 头。这些方法通过 `HttpServletRequest` 对象可用。

| 序号 | 方法 & 描述                                                  |
| :--: | :----------------------------------------------------------- |
|  1   | `Cookie[] getCookies()` 返回一个数组，包含客户端发送该请求的所有的 Cookie 对象。 |
|  2   | `Enumeration getAttributeNames()` 返回一个枚举，包含提供给该请求可用的属性名称。 |
|  3   | `Enumeration getHeaderNames()` 返回一个枚举，包含在该请求中包含的所有的头名。 |
|  4   | `Enumeration getParameterNames()` 返回一个 String 对象的枚举，包含在该请求中包含的参数的名称。 |
|  5   | `HttpSession getSession()` 返回与该请求关联的当前 session 会话，或者如果请求没有 session 会话，则创建一个。 |
|  6   | `HttpSession getSession(boolean create)` 返回与该请求关联的当前 HttpSession，或者如果没有当前会话，且创建是真的，则返回一个新的 session 会话。 |
|  7   | `Locale getLocale()` 基于 Accept-Language 头，返回客户端接受内容的首选的区域设置。 |
|  8   | `Object getAttribute(String name)` 以对象形式返回已命名属性的值，如果没有给定名称的属性存在，则返回 null。 |
|  9   | `ServletInputStream getInputStream()` 使用 ServletInputStream，以二进制数据形式检索请求的主体。 |
|  10  | `String getAuthType()` 返回用于保护 Servlet 的身份验证方案的名称，例如，"BASIC" 或 "SSL"，如果 JSP 没有受到保护则返回 null。 |
|  11  | `String getCharacterEncoding()` 返回请求主体中使用的字符编码的名称。 |
|  12  | `String getContentType()` 返回请求主体的 MIME 类型，如果不知道类型则返回 null。 |
|  13  | `String getContextPath()` 返回指示请求上下文的请求 URI 部分。 |
|  14  | `String getHeader(String name)` 以字符串形式返回指定的请求头的值。 |
|  15  | `String getMethod()` 返回请求的 HTTP 方法的名称，例如，GET、POST 或 PUT。 |
|  16  | `String getParameter(String name)` 以字符串形式返回请求参数的值，或者如果参数不存在则返回 null。 |
|  17  | `String getPathInfo()` 当请求发出时，返回与客户端发送的 URL 相关的任何额外的路径信息。 |
|  18  | `String getProtocol()` 返回请求协议的名称和版本。            |
|  19  | `String getQueryString()` 返回包含在路径后的请求 URL 中的查询字符串。 |
|  20  | `String getRemoteAddr()` 返回发送请求的客户端的互联网协议（IP）地址。 |
|  21  | `String getRemoteHost()` 返回发送请求的客户端的完全限定名称。 |
|  22  | `String getRemoteUser()` 如果用户已通过身份验证，则返回发出请求的登录用户，或者如果用户未通过身份验证，则返回 `null`。 |
|  23  | `String getRequestURI()` 从协议名称直到 HTTP 请求的第一行的查询字符串中，返回该请求的 URL 的一部分。 |
|  24  | `String getRequestedSessionId()` 返回由客户端指定的 session 会话 ID。 |
|  25  | `String getServletPath()` 返回调用 JSP 的请求的 URL 的一部分。 |
|  26  | `String[] getParameterValues(String name)` 返回一个字符串对象的数组，包含所有给定的请求参数的值，如果参数不存在则返回 null。 |
|  27  | `boolean isSecure()` 返回一个布尔值，指示请求是否使用安全通道，如 HTTPS。 |
|  28  | `int getContentLength()` 以字节为单位返回请求主体的长度，并提供输入流，或者如果长度未知则返回 `-1`。 |
|  29  | `int getIntHeader(String name)` 返回指定的请求头的值为一个 int 值。 |
|  30  | `int getServerPort()` 返回接收到这个请求的端口号。           |
|  31  | `int getParameterMap()` 将参数封装成 Map 类型。              |

6.2 HTTP Header 请求实例

`DisplayHeader.java`

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/DisplayHeader")

// 扩展 HttpServlet 类
public class DisplayHeader extends HttpServlet {
    // 处理 GET 方法请求的方法
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String title = "HTTP Header 请求实例 - 菜鸟教程实例";
        String docType =
                "<!DOCTYPE html> \n";
        out.println(docType +
                "<html>\n" +
                "<head><meta charset=\"utf-8\"><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<table width=\"100%\" border=\"1\" align=\"center\">\n" +
                "<tr bgcolor=\"#949494\">\n" +
                "<th>Header 名称</th><th>Header 值</th>\n" +
                "</tr>\n");

        Enumeration headerNames = request.getHeaderNames();
        while (headerNames.hasMoreElements()) {
            String paramName = (String) headerNames.nextElement();
            out.print("<tr><td>" + paramName + "</td>\n");
            String paramValue = request.getHeader(paramName);
            out.println("<td> " + paramValue + "</td></tr>\n");
        }
        out.println("</table>\n</body></html>");
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

​	对应 `web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
    <servlet>
        <!-- 类名 -->
        <servlet-name>DisplayHeader</servlet-name>
        <!-- 所在的包 -->
        <servlet-class>com.runoob.test.DisplayHeader</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>DisplayHeader</servlet-name>
        <!-- 访问的网址 -->
        <url-pattern>/TomcatTest/DisplayHeader</url-pattern>
    </servlet-mapping>
</web-app>
```

### 7 Servlet 服务器 HTTP 响应

​	典型的响应报文

```http
HTTP/1.1 200 OK
Content-Type: text/html
Header2: ...
...
HeaderN: ...
  (Blank Line)
<!doctype ...>
<html>
<head>...</head>
<body>
...
</body>
</html>
```

​	其中，HTTP/1.1 响应报头：

| 头信息                | 描述                                                         |
| :-------------------- | :----------------------------------------------------------- |
| `Allow`               | 这个头信息指定服务器支持的请求方法（GET、POST 等）。         |
| `Cache-Control`       | 这个头信息指定响应文档在何种情况下可以安全地缓存。可能的值有：**public、private** 或 **no-cache** 等。Public 意味着文档是可缓存，Private 意味着文档是单个用户私用文档，且只能存储在私有（非共享）缓存中，no-cache 意味着文档不应被缓存。 |
| `Connection`          | 这个头信息指示浏览器是否使用持久 HTTP 连接。值 **close** 指示浏览器不使用持久 HTTP 连接，值 **keep-alive** 意味着使用持久连接。 |
| `Content-Disposition` | 这个头信息可以让您请求浏览器要求用户以给定名称的文件把响应保存到磁盘。 |
| `Content-Encoding`    | 在传输过程中，这个头信息指定页面的编码方式。                 |
| `Content-Language`    | 这个头信息表示文档编写所使用的语言。例如，en、en-us、ru 等。 |
| `Content-Length`      | 这个头信息指示响应中的字节数。只有当浏览器使用持久（keep-alive）HTTP 连接时才需要这些信息。 |
| `Content-Type`        | 这个头信息提供了响应文档的 MIME（Multipurpose Internet Mail Extension）类型。 |
| `Expires`             | 这个头信息指定内容过期的时间，在这之后内容不再被缓存。       |
| `Last-Modified`       | 这个头信息指示文档的最后修改时间。然后，客户端可以缓存文件，并在以后的请求中通过 **If-Modified-Since** 请求头信息提供一个日期。 |
| `Location`            | 这个头信息应被包含在所有的带有状态码的响应中。在 300s 内，这会通知浏览器文档的地址。浏览器会自动重新连接到这个位置，并获取新的文档。 |
| `Refresh`             | 这个头信息指定浏览器应该如何尽快请求更新的页面。您可以指定页面刷新的秒数。 |
| `Retry-After`         | 这个头信息可以与 503（Service Unavailable 服务不可用）响应配合使用，这会告诉客户端多久就可以重复它的请求。 |
| `Set-Cookie`          | 这个头信息指定一个与页面关联的 cookie。                      |

#### 7.1 设置 HTTP 响应报头的方法

​	下面的方法可用于在 Servlet 程序中设置 HTTP 响应报头。

​	这些方法通过 `HttpServletResponse` 对象可用。

| 序号 | 方法 & 描述                                                  |
| ---- | ------------------------------------------------------------ |
| 1    | `String encodeRedirectURL(String url)` 为 sendRedirect 方法中使用的指定的 URL 进行编码，或者如果编码不是必需的，则返回 URL 未改变。 |
| 2    | `String encodeURL(String url)` 对包含 session 会话 ID 的指定 URL 进行编码，或者如果编码不是必需的，则返回 URL 未改变。 |
| 3    | `boolean containsHeader(String name)` 返回一个布尔值，指示是否已经设置已命名的响应报头。 |
| 4    | `boolean isCommitted()` 返回一个布尔值，指示响应是否已经提交。 |
| 5    | `void addCookie(Cookie cookie)` 把指定的 cookie 添加到响应。 |
| 6    | `void addDateHeader(String name, long date)` 添加一个带有给定的名称和日期值的响应报头。 |
| 7    | `void addHeader(String name, String value)` 添加一个带有给定的名称和值的响应报头。 |
| 8    | `void addIntHeader(String name, int value)` 添加一个带有给定的名称和整数值的响应报头。 |
| 9    | `void flushBuffer()` 强制任何在缓冲区中的内容被写入到客户端。 |
| 10   | `void reset()` 清除缓冲区中存在的任何数据，包括状态码和头。  |
| 11   | `void resetBuffer()` 清除响应中基础缓冲区的内容，不清除状态码和头。 |
| 12   | `void sendError(int sc)` 使用指定的状态码发送错误响应到客户端，并清除缓冲区。 |
| 13   | `void sendError(int sc, String msg)` 使用指定的状态发送错误响应到客户端。 |
| 14   | `void sendRedirect(String location)` 使用指定的重定向位置 URL 发送临时重定向响应到客户端。 |
| 15   | `void setBufferSize(int size)` 为响应主体设置首选的缓冲区大小。 |
| 16   | `void setCharacterEncoding(String charset)` 设置被发送到客户端的响应的字符编码（MIME 字符集）例如，UTF-8。 |
| 17   | `void setContentLength(int len)` 设置在 HTTP Servlet 响应中的内容主体的长度，该方法设置 HTTP Content-Length 头。 |
| 18   | `void setContentType(String type)` 如果响应还未被提交，设置被发送到客户端的响应的内容类型。 |
| 19   | `void setDateHeader(String name, long date)` 设置一个带有给定的名称和日期值的响应报头。 |
| 20   | `void setHeader(String name, String value)` 设置一个带有给定的名称和值的响应报头。 |
| 21   | `void setIntHeader(String name, int value)` 设置一个带有给定的名称和整数值的响应报头。 |
| 22   | `void setLocale(Locale loc)` 如果响应还未被提交，设置响应的区域。 |
| 23   | `void setStatus(int sc)` 为该响应设置状态码。                |

#### 7.2 HTTP Header 响应实例

`Refresh.java`

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/Refresh")

// 扩展 HttpServlet 类
public class Refresh extends HttpServlet {
    // 处理 GET 方法请求的方法
    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置刷新自动加载时间为 5 秒
        response.setIntHeader("Refresh", 5);
        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");

        // 使用默认时区和语言环境获得一个日历
        Calendar cale = Calendar.getInstance();
        // 将Calendar类型转换成Date类型
        Date tasktime = cale.getTime();
        // 设置日期输出的格式
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        //格式化输出
        String nowTime = df.format(tasktime);
        PrintWriter out = response.getWriter();
        String title = "自动刷新 Header 设置 - 菜鸟教程实例";
        String docType =
                "<!DOCTYPE html>\n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<p>当前时间是：" + nowTime + "</p>\n");
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request,
                       HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }
}
```

​	配置 `web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<web-app>  
  <servlet>  
     <!-- 类名 -->  
    <servlet-name>Refresh</servlet-name>  
    <!-- 所在的包 -->  
    <servlet-class>com.runoob.test.Refresh</servlet-class>  
  </servlet>  
  <servlet-mapping>  
    <servlet-name>Refresh</servlet-name>  
    <!-- 访问的网址 -->  
    <url-pattern>/TomcatTest/Refresh</url-pattern>  
    </servlet-mapping>  
</web-app>
```

### 8 Servlet HTTP 状态码

​	HTTP 请求和 HTTP 响应消息的格式是类似的，结构如下：

- 初始状态行 + 回车换行符（回车+换行）
- 零个或多个标题行+回车换行符
- 一个空白行，即回车换行符
- 一个可选的消息主体，比如文件、查询数据或查询输出

以下是可能从 Web 服务器返回的 HTTP 状态码和相关的信息列表：

| 代码 |             消息              | 描述                                                         |
| :--: | :---------------------------: | ------------------------------------------------------------ |
| 100  |           Continue            | 只有请求的一部分已经被服务器接收，但只要它没有被拒绝，客户端应继续该请求。 |
| 101  |      Switching Protocols      | 服务器切换协议。                                             |
| 200  |              OK               | 请求成功。                                                   |
| 201  |            Created            | 该请求是完整的，并创建一个新的资源。                         |
| 202  |           Accepted            | 该请求被接受处理，但是该处理是不完整的。                     |
| 203  | Non-authoritative Information |                                                              |
| 204  |          No Content           |                                                              |
| 205  |         Reset Content         |                                                              |
| 206  |        Partial Content        |                                                              |
| 300  |       Multiple Choices        | 链接列表。用户可以选择一个链接，进入到该位置。最多五个地址。 |
| 301  |       Moved Permanently       | 所请求的页面已经转移到一个新的 URL。                         |
| 302  |             Found             | 所请求的页面已经临时转移到一个新的 URL。                     |
| 303  |           See Other           | 所请求的页面可以在另一个不同的 URL 下被找到。                |
| 304  |         Not Modified          |                                                              |
| 305  |           Use Proxy           |                                                              |
| 306  |           *Unused*            | 在以前的版本中使用该代码。现在已不再使用它，但代码仍被保留。 |
| 307  |      Temporary Redirect       | 所请求的页面已经临时转移到一个新的 URL。                     |
| 400  |          Bad Request          | 服务器不理解请求。                                           |
| 401  |         Unauthorized          | 所请求的页面需要用户名和密码。                               |
| 402  |       Payment Required        | *您还不能使用该代码。*                                       |
| 403  |           Forbidden           | 禁止访问所请求的页面。                                       |
| 404  |           Not Found           | 服务器无法找到所请求的页面。.                                |
| 405  |      Method Not Allowed       | 在请求中指定的方法是不允许的。                               |
| 406  |        Not Acceptable         | 服务器只生成一个不被客户端接受的响应。                       |
| 407  | Proxy Authentication Required | 在请求送达之前，您必须使用代理服务器的验证。                 |
| 408  |        Request Timeout        | 请求需要的时间比服务器能够等待的时间长，超时。               |
| 409  |           Conflict            | 请求因为冲突无法完成。                                       |
| 410  |             Gone              | 所请求的页面不再可用。                                       |
| 411  |        Length Required        | "Content-Length" 未定义。服务器无法处理客户端发送的不带 Content-Length 的请求信息。 |
| 412  |      Precondition Failed      | 请求中给出的先决条件被服务器评估为 false。                   |
| 413  |   Request Entity Too Large    | 服务器不接受该请求，因为请求实体过大。                       |
| 414  |     Request-url Too Long      | 服务器不接受该请求，因为 URL 太长。当您转换一个 "post" 请求为一个带有长的查询信息的 "get" 请求时发生。 |
| 415  |    Unsupported Media Type     | 服务器不接受该请求，因为媒体类型不被支持。                   |
| 417  |      Expectation Failed       |                                                              |
| 500  |     Internal Server Error     | 未完成的请求。服务器遇到了一个意外的情况。                   |
| 501  |        Not Implemented        | 未完成的请求。服务器不支持所需的功能。                       |
| 502  |          Bad Gateway          | 未完成的请求。服务器从上游服务器收到无效响应。               |
| 503  |      Service Unavailable      | 未完成的请求。服务器暂时超载或死机。                         |
| 504  |        Gateway Timeout        | 网关超时。                                                   |
| 505  |  HTTP Version Not Supported   | 服务器不支持"HTTP协议"版本。                                 |

#### 8.1 设置 HTTP 状态代码的方法

​	下面的方法可用于在 Servlet 程序中设置 HTTP 状态码。这些方法通过 `HttpServletResponse` 对象可用。

| 序号 | 方法 & 描述                                                  |
| :--: | ------------------------------------------------------------ |
|  1   | `public void setStatus ( int statusCode )` 该方法设置一个任意的状态码。setStatus 方法接受一个 int（状态码）作为参数。如果您的反应包含了一个特殊的状态码和文档，请确保在使用 *PrintWriter* 实际返回任何内容之前调用 setStatus。 |
|  2   | `public void sendRedirect(String url)` 该方法生成一个 302 响应，连同一个带有新文档 URL 的 *Location* 头。 |
|  3   | `public void sendError(int code, String message)` 该方法发送一个状态码（通常为 404），连同一个在 HTML 文档内部自动格式化并发送到客户端的短消息。 |

#### 8.2  HTTP 状态码实例

​	下面的例子把 `407` 错误代码发送到客户端浏览器，浏览器会显示 "Need authentication!!!" 消息。

```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
import java.util.*;

@WebServlet("/showError")
// 扩展 HttpServlet 类
public class showError extends HttpServlet {
    // 处理 GET 方法请求的方法
    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException {
        // 设置错误代码和原因
        response.sendError(407, "Need authentication!!!");
    }

    // 处理 POST 方法请求的方法
    public void doPost(HttpServletRequest request,
                       HttpServletResponse response)
            throws ServletException, IOException {
        doGet(request, response);
    }
}
```

### 9 Servlet 编写过滤器

​	Servlet 过滤器可以动态地拦截请求和响应，以变换或使用包含在请求或响应中的信息。

#### 9.1 Servlet 过滤器方法

​	过滤器是一个实现了 `javax.servlet.Filter` 接口的 Java 类。`javax.servlet.Filter` 接口定义了三个方法：

| 序号 | 方法 & 描述                                                  |
| :--: | ------------------------------------------------------------ |
|  1   | `public void doFilter (ServletRequest, ServletResponse, FilterChain)` 该方法完成实际的过滤操作，当客户端请求方法与过滤器设置匹配的 URL 时，Servlet 容器将先调用过滤器的 doFilter 方法。FilterChain 用户访问后续过滤器。 |
|  2   | `public void init(FilterConfig filterConfig)` Web 应用程序启动时，Web 服务器将创建Filter 的实例对象，并调用其init方法，读取web.xml配置，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作（filter对象只会创建一次，init方法也只会执行一次）。开发人员通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。 |
|  3   | `public void destroy()` Servlet 容器在销毁过滤器实例前调用该方法，在该方法中释放Servlet过滤器占用的资源。 |

##### FilterConfig 使用

​	Filter 的 init 方法中提供了一个 FilterConfig 对象。

​	web.xml

```xml
<filter>
	<filter-name>LogFilter</filter-name>
    <filter-class>com.runoob.test.LogFilter</filter-class>
    <init-param>
        <param-name>Site</param-name>
        <param-value>菜鸟教程</param-value>
    </init-param>
</filter>
```

​	在 init 方法使用 `FilterConfig` 对象获取参数：

```java
public void init(FilterConfig config) throws ServletException {
    // 获取初始化参数
    String site = config.getInitParameter("Site"); 
    // 输出初始化参数
    System.out.println("网站名称: " + site); 
}
```

#### 9.2 Servlet 过滤器实例

```java
package com.runoob.test;

// 导入必需的 java 库
import javax.servlet.*;
import java.util.*;

// 实现 Filter 类
public class LogFilter implements Filter {
    public void init(FilterConfig config) throws ServletException {
        // 获取初始化参数
        String site = config.getInitParameter("Site");

        // 输出初始化参数
        System.out.println("网站名称: " + site);
    }

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws java.io.IOException, ServletException {

        // 输出站点名称
        System.out.println("站点网址：http://www.runoob.com");

        // 把请求传回过滤链
        chain.doFilter(request, response);
    }

    public void destroy() {
        /* 在 Filter 实例被 Web 容器从服务移除之前调用 */
    }
}
```

### 10 Servlet 异常处理

​	当一个 Servlet 抛出一个异常时，Web 容器在使用了 exception-type 元素的 **web.xml** 中搜索与抛出异常类型相匹配的配置。

​	您必须在 web.xml 中使用 **error-page** 元素来指定对特定**异常** 或 HTTP **状态码** 作出相应的 Servlet 调用。

#### 10.1 `web.xml` 配置

#### 10.2 请求属性 - 错误/异常

​	以下是错误处理的 Servlet 可以访问的请求属性列表，用来分析错误/异常的性质。

| 序号 | 属性 & 描述                                                  |
| :--: | ------------------------------------------------------------ |
|  1   | `javax.servlet.error.status_code` 该属性给出状态码，状态码可被存储，并在存储为 java.lang.Integer 数据类型后可被分析。 |
|  2   | `javax.servlet.error.exception_type` 该属性给出异常类型的信息，异常类型可被存储，并在存储为 java.lang.Class 数据类型后可被分析。 |
|  3   | `javax.servlet.error.message` 该属性给出确切错误消息的信息，信息可被存储，并在存储为 java.lang.String 数据类型后可被分析。 |
|  4   | `javax.servlet.error.request_uri` 该属性给出有关 URL 调用 Servlet 的信息，信息可被存储，并在存储为 java.lang.String 数据类型后可被分析。 |
|  5   | `javax.servlet.error.exception` 该属性给出异常产生的信息，信息可被存储，并在存储为 java.lang.Throwable 数据类型后可被分析。 |
|  6   | `javax.servlet.error.servlet_name` 该属性给出 Servlet 的名称，名称可被存储，并在存储为 java.lang.String 数据类型后可被分析。 |

#### 10.3 Servlet 错误处理程序实例

### 11 Servlet Cookie 处理

​	Cookie 是存储在客户端计算机上的文本文件，并保留了各种跟踪信息。Java Servlet 显然支持 HTTP Cookie。

#### 11.1 Cookie 剖析

​	Cookie 通常设置在 HTTP 头信息中（虽然 JavaScript 也可以直接在浏览器上设置一个 Cookie）。设置 Cookie 的 Servlet 会发送如下的头信息：

```http
HTTP/1.1 200 OK
Date: Fri, 04 Feb 2000 21:03:38 GMT
Server: Apache/1.3.9 (UNIX) PHP/4.0b3
Set-Cookie: name=xyz; expires=Friday, 04-Feb-07 22:03:38 GMT; 
                 path=/; domain=runoob.com
Connection: close
Content-Type: text/html
```

​	浏览器的头信息可能如下所示：

```java
GET / HTTP/1.0
Connection: Keep-Alive
User-Agent: Mozilla/4.6 (X11; I; Linux 2.2.6-15apmac ppc)
Host: zink.demon.co.uk:1126
Accept: image/gif, */*
Accept-Encoding: gzip
Accept-Language: en
Accept-Charset: iso-8859-1,*,utf-8
Cookie: name=xyz
```

#### 11.2 Servlet Cookie 方法

| 序号 | 方法 & 描述                                                  |
| :--: | ------------------------------------------------------------ |
|  1   | `public void setDomain(String pattern)` 该方法设置 cookie 适用的域，例如 runoob.com。 |
|  2   | `public String getDomain()` 该方法获取 cookie 适用的域，例如 runoob.com。 |
|  3   | `public void setMaxAge(int expiry)` 该方法设置 cookie 过期的时间（以秒为单位）。如果不这样设置，cookie 只会在当前 session 会话中持续有效。 |
|  4   | `public int getMaxAge()` 该方法返回 cookie 的最大生存周期（以秒为单位），默认情况下，-1 表示 cookie 将持续下去，直到浏览器关闭。 |
|  5   | `public String getName()` 该方法返回 cookie 的名称。名称在创建后不能改变。 |
|  6   | `public void setValue(String newValue)` 该方法设置与 cookie 关联的值。 |
|  7   | `public String getValue()` 该方法获取与 cookie 关联的值。    |
|  8   | `public void setPath(String uri)` 该方法设置 cookie 适用的路径。如果您不指定路径，与当前页面相同目录下的（包括子目录下的）所有 URL 都会返回 cookie。 |
|  9   | `public String getPath()` 该方法获取 cookie 适用的路径。     |
|  10  | `public void setSecure(boolean flag)` 该方法设置布尔值，表示 cookie 是否应该只在加密的（即 SSL）连接上发送。 |
|  11  | `public void setComment(String purpose)` 设置cookie的注释。该注释在浏览器向用户呈现 cookie 时非常有用。 |
|  12  | `public String getComment()` 获取 cookie 的注释，如果 cookie 没有注释则返回 null。 |

#### 11.3 透过 Servlet 设置 Cookie

##### 1 创建一个 Cookie 对象

```java
Cookie cookie = new Cookie("key", "value");
```

##### 2 设置最大生存周期

```java
cookie.setMaxAge(60 * 60 * 24);
```

##### 3 发送 Cookie 到 HTTP 响应头

```
response.addCookie(cookie);
```

#### 11.4 实例

### 12 Servlet Session 跟踪

​	HTTP 是一种「无状态」协议，这意味着每次客户端检索网页时，客户端打开一个单独的连接到 Web 服务器，服务器会自动不保留之前客户端请求的任何记录。

​	无状态是指协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。即我们给服务器发送 HTTP 请求之后，服务器根据请求，会给我们发送数据过来，但是，发送完，不会记录任何信息。  

​	HTTP 是一个无状态协议，这意味着每个请求都是独立的，Keep-Alive 没能改变这个结果。

#### 12.1 Cookies

​	一个 Web 服务器可以分配一个唯一的 session 会话 ID 作为每个 Web 客户端的 cookie，对于客户端的后续请求可以使用接收到的 cookie 来识别。

​	这可能不是一个有效的方法，因为很多浏览器不支持 cookie，所以我们建议不要使用这种方式来维持 session 会话。

#### 12.2 隐藏的表单字段

​	一个 Web 服务器可以发送一个隐藏的 HTML 表单字段，以及一个唯一的 session 会话 ID：

```html
<input type="hidden" name="sessionid" value="12345">
```

#### 12.3 URL 重写

​	URL 重写是一种更好的维持 session 会话的方式，它在浏览器不支持 cookie 时能够很好地工作，但是它的缺点是会动态生成每个 URL 来为页面分配一个 session 会话 ID，即使是在很简单的静态 HTML 页面中也会如此。

#### 12.4 HttpSession 对象

​	除了上述的三种方式，Servlet 还提供了 HttpSession 接口，该接口提供了一种跨多个页面请求或访问网站时识别用户以及存储有关用户信息的方式。

```java
HttpSession session = request.getSession();
```

`Request.getSession()`

| 序号 | 方法 & 描述                                                  |
| :--: | ------------------------------------------------------------ |
|  1   | `public Object getAttribute(String name)` 该方法返回在该 session 会话中具有指定名称的对象，如果没有指定名称的对象，则返回 null。 |
|  2   | `public Enumeration getAttributeNames()` 该方法返回 String 对象的枚举，String 对象包含所有绑定到该 session 会话的对象的名称。 |
|  3   | `public long getCreationTime()` 该方法返回该 session 会话被创建的时间，自格林尼治标准时间 1970 年 1 月 1 日午夜算起，以毫秒为单位。 |
|  4   | `public String getId()` 该方法返回一个包含分配给该 session 会话的唯一标识符的字符串。 |
|  5   | `public long getLastAccessedTime()` 该方法返回客户端最后一次发送与该 session 会话相关的请求的时间自格林尼治标准时间 1970 年 1 月 1 日午夜算起，以毫秒为单位。 |
|  6   | `public int getMaxInactiveInterval()` 该方法返回 Servlet 容器在客户端访问时保持 session 会话打开的最大时间间隔，以秒为单位。 |
|  7   | `public void invalidate()` 该方法指示该 session 会话无效，并解除绑定到它上面的任何对象。 |
|  8   | `public boolean isNew()` 如果客户端还不知道该 session 会话，或者如果客户选择不参入该 session 会话，则该方法返回 true。 |
|  9   | `public void removeAttribute(String name)` 该方法将从该 session 会话移除指定名称的对象。 |
|  10  | `public void setAttribute(String name, Object value)`  该方法使用指定的名称绑定一个对象到该 session 会话。 |
|  11  | `public void setMaxInactiveInterval(int interval)` 该方法在 Servlet 容器指示该 session 会话无效之前，指定客户端请求之间的时间，以秒为单位。 |

#### 12.5 Session 跟踪实例

##### 12.5.1 项目结构

```powershell
.
├── .DS_Store
├── .idea
│   ├── artifacts
│   │   └── ServletDemo_war_exploded.xml
│   ├── misc.xml
│   ├── modules.xml
│   └── workspace.xml
├── ServletDemo.iml
├── out
│   ├── artifacts
│   │   └── ServletDemo_war_exploded
│   │       ├── WEB-INF
│   │       │   ├── classes
│   │       │   │   └── com
│   │       │   │       └── runoob
│   │       │   │           └── test
│   │       │   │               └── SessionTrack.class
│   │       │   └── web.xml
│   │       └── index.jsp
│   └── production
│       └── ServletDemo
│           └── com
│               └── runoob
│                   └── test
│                       └── SessionTrack.class
├── src
│   ├── .DS_Store
│   └── com
│       └── runoob
│           └── test
│               └── SessionTrack.java
└── web
    ├── WEB-INF
    │   └── web.xml
    └── index.jsp
```

##### 12.5.2 `SessionTrack.java`

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/**
 * Servlet implementation class SessionTrack
 */
@WebServlet("/SessionTrack")
public class SessionTrack extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 如果不存在 session 会话，则创建一个 session 对象
        HttpSession session = request.getSession(true);
        // 获取 session 创建时间
        Date createTime = new Date(session.getCreationTime());
        // 获取该网页的最后一次访问时间
        Date lastAccessTime = new Date(session.getLastAccessedTime());

        //设置日期输出的格式
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

        String title = "Servlet Session 实例 - 菜鸟教程";
        Integer visitCount = new Integer(0);
        String visitCountKey = new String("visitCount");
        String userIDKey = new String("userID");
        String userID = new String("Runoob");

        // 检查网页上是否有新的访问者
        if (session.isNew()) {
            title = "Servlet Session 实例 - 菜鸟教程";
            session.setAttribute(userIDKey, userID);
        } else {
            visitCount = (Integer) session.getAttribute(visitCountKey);
            visitCount = visitCount + 1;
            userID = (String) session.getAttribute(userIDKey);
        }
        session.setAttribute(visitCountKey, visitCount);

        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        String docType = "<!DOCTYPE html>\n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n" +
                "<h2 align=\"center\">Session 信息</h2>\n" +
                "<table border=\"1\" align=\"center\">\n" +
                "<tr bgcolor=\"#949494\">\n" +
                "  <th>Session 信息</th><th>值</th></tr>\n" +
                "<tr>\n" +
                "  <td>id</td>\n" +
                "  <td>" + session.getId() + "</td></tr>\n" +
                "<tr>\n" +
                "  <td>创建时间</td>\n" +
                "  <td>" + df.format(createTime) +
                "  </td></tr>\n" +
                "<tr>\n" +
                "  <td>最后访问时间</td>\n" +
                "  <td>" + df.format(lastAccessTime) +
                "  </td></tr>\n" +
                "<tr>\n" +
                "  <td>用户 ID</td>\n" +
                "  <td>" + userID +
                "  </td></tr>\n" +
                "<tr>\n" +
                "  <td>访问统计：</td>\n" +
                "  <td>" + visitCount + "</td></tr>\n" +
                "</table>\n" +
                "</body></html>");
    }
}
```

##### 12.5.3 配置 web.xml 的条目

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>SessionTrack</servlet-name>
        <servlet-class>com.runoob.test.SessionTrack</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>SessionTrack</servlet-name>
        <url-pattern></url-pattern>
    </servlet-mapping>
</web-app>
```

##### 12.5.4 运行并访问 `http://localhost:8080/`

![icon](/Users/lightwingng/Desktop/img/ServletSessionExcample.png)

### 13 Servlet 数据库的访问

#### 13.1 创建测试数据

```powershell
mysql> select * from websites order by alexa;
+----+-----------+----------------------------+-------+---------+
| id | name      | url                        | alexa | country |
+----+-----------+----------------------------+-------+---------+
|  1 | Google    | https://www.google.cm/     |     1 | USA     |
|  6 | YouTube   | https://www.youtube.com/   |     2 | USA     |
|  5 | Facebook  | https://www.facebook.com/  |     3 | USA     |
|  7 | Baidu     | https://www.baidu.com      |     4 | CN      |
|  8 | Wikipedia | https://www.wikipedia.org/ |     5 | USA     |
|  9 | Tecent    | https://www.qq.com         |     7 | CN      |
|  2 | Taobao    | https://www.taobao.com/    |    13 | CN      |
|  4 | Weibo     | https://weibo.com/         |    20 | CN      |
|  3 | Runoob    | http://www.runoob.com      |  5892 | CN      |
+----+-----------+----------------------------+-------+---------+
9 rows in set (0.00 sec)
```

#### 13.2 使用 Servlet 访问 User 数据库

```java
package com.runoob.test;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class DatabaseAccess
 */
@WebServlet("/DatabaseAccess")
public class DatabaseAccess extends HttpServlet {
    private static final long serialVersionUID = 1L;

    // JDBC 驱动名及数据库 URL
    static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
    static final String DB_URL = "jdbc:mysql://localhost:3306/User";

    // 数据库的用户名与密码，需要根据自己的设置
    static final String USER = "root";
    static final String PASS = "**********";

    /**
     * @see HttpServlet#HttpServlet()
     */
    public DatabaseAccess() {
        super();
        // TODO Auto-generated constructor stub
    }

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Connection conn = null;
        Statement stmt = null;

        // 设置响应内容类型
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        String title = "Servlet Mysql 测试 - 菜鸟教程";
        String docType = "<!DOCTYPE html>\n";
        out.println(docType +
                "<html>\n" +
                "<head><title>" + title + "</title></head>\n" +
                "<body bgcolor=\"#f0f0f0\">\n" +
                "<h1 align=\"center\">" + title + "</h1>\n");
        try {
            // 注册 JDBC 驱动器
            Class.forName("com.mysql.jdbc.Driver");

            // 打开一个连接
            conn = DriverManager.getConnection(DB_URL, USER, PASS);

            // 执行 SQL 查询
            stmt = conn.createStatement();
            String sql = "SELECT id, name, url FROM websites";
            ResultSet rs = stmt.executeQuery(sql);

            // 展开结果集数据库
            while (rs.next()) {
                // 通过字段检索
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String url = rs.getString("url");

                System.out.println(url);

                // 输出数据
                out.println("ID: " + id);
                out.println(", 站点名称: " + name);
                out.println(", 站点 URL: " + url);
                out.println("<br />");
            }
            out.println("</body></html>");

            // 完成后关闭
            rs.close();
            stmt.close();
            conn.close();
        } catch (SQLException se) {
            // 处理 JDBC 错误
            se.printStackTrace();
        } catch (Exception e) {
            // 处理 Class.forName 错误
            e.printStackTrace();
        } finally {
            // 最后是用于关闭资源的块
            try {
                if (stmt != null)
                    stmt.close();
            } catch (SQLException se2) {
            }
            try {
                if (conn != null)
                    conn.close();
            } catch (SQLException se) {
                se.printStackTrace();
            }
        }

    }

    /**
     * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // TODO Auto-generated method stub
        doGet(request, response);
    }
}
```

#### 13.3 设置 web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>DatabaseAccess</servlet-name>
        <servlet-class>com.runoob.test.DatabaseAccess</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>DatabaseAccess</servlet-name>
        <url-pattern></url-pattern>
    </servlet-mapping>
</web-app>
```