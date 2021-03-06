# 一、Servlet

Servlet接口在Sun公司有两个默认的实现类：HttpServlet和GenericServlet

## 1.1、Servlet简介

- Servlet是sun公司开发动态web的一门技术
- Sun公司在这些API中提供一个接口，叫做Servlet。如果想开发一个Servlet程序，只需要完成两个步骤：
  - 编写一个类，实现Servlet接口
  - 把开发好的Java类部署到Web服务器中

把实现了Servlet接口的Java程序叫做Servlet



## 1.2、Hello Servlet

1. 构建一个普通的Maven项目，删掉里面的`src`目录，以后的项目都在这个里面建立`Moudel`，这个空的工程就是Maven主工程

2. 关于Maven父子工程的理解：

   父项目中会有

   ```xml
   <modules>
       <module>servlet-01</module>
   </modules>
   ```

   子项目中会有

   ```xml
   <parent>
       <artifactId>javaweb-02-servlet</artifactId>
       <groupId>com.study</groupId>
       <version>1.0-SNAPSHOT</version>
   </parent>
   ```

   父项目中的java子项目可以直接使用

   ```java
   // 用java中的关系来描述：就是继承
   son extends father 
   ```

3. Maven环境优化

   1. 修改`web.xml`为最新的
   2. 将maven的结构搭建完整

4. 编写一个Servlet程序

   1. 编写一个普通类
   2. 实现Servlet接口，这里直接继承HttpServlet

   ![1635772350406](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635772350406.png)

   3. 代码

      ```java
      public class HelloServlet extends HttpServlet {
          // 由于 Get或者 Post只是请求实现的不同方式，可以相互调用，业务逻辑都一样
          @Override
          protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      //        ServletOutputStream outputStream = resp.getOutputStream();
              PrintWriter writer = resp.getWriter(); // 响应流
              writer.print("Hello Servlet");
          }
      
          @Override
          protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
              doGet(req, resp);
          }
      }
      ```

5. 编写Servlet的映射

   为什么需要映射：我们写的是JAVA程序，但是要通过浏览器访问，而浏览器需要连接web服务器，所以我们需要在web服务中注册我们写的Servlet，还需要给它一个浏览器能够访问的路径

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee     http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
            version="3.1">
       
       <!-- 注册Servlet -->
       <servlet>
           <servlet-name>hello</servlet-name>
           <servlet-class>com.study.servlet.HelloServlet</servlet-class>
       </servlet>
       
       <!-- Servlet的请求路径 -->
       <servlet-mapping>
           <servlet-name>hello</servlet-name>
           <url-pattern>/hello</url-pattern>
       </servlet-mapping>
       
   </web-app>
   ```

   

6. 配置Tomcat

   注意：配置项目发布的路径就可以了

7. 启动测试



## 1.3、Servlet原理

Servlet是由Web服务器调用，Web服务器在收到浏览器请求后，会：

![1635778386198](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635778386198.png)



## 1.4、Mapping

1. 一个Servlet可以指定一个映射路径

   ```xml
   <!-- 注册Servlet -->
   <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>com.study.servlet.HelloServlet</servlet-class>
   </servlet>
   
   <!-- Servlet的请求路径 -->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
   </servlet-mapping>
   ```

2. 一个Servlet可以指定多个映射路径

   ```xml
   <!-- 注册Servlet -->
   <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>com.study.servlet.HelloServlet</servlet-class>
   </servlet>
   
   <!-- Servlet的请求路径 -->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
   </servlet-mapping>
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello1</url-pattern>
   </servlet-mapping>
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello2</url-pattern>
   </servlet-mapping>
   ```

3. 一个Servlet可以指定通用映射路径

   ```xml
   <!-- 注册Servlet -->
   <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>com.study.servlet.HelloServlet</servlet-class>
   </servlet>
   
   <!-- Servlet的请求路径 -->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello/*</url-pattern>
   </servlet-mapping>
   
   <!-- 默认请求，会替代掉index.xml -->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/*</url-pattern>
   </servlet-mapping>
   ```

4. 指定一些后缀或者前缀等等...

   ```xml
   <!-- 注册Servlet -->
   <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>com.study.servlet.HelloServlet</servlet-class>
   </servlet>
   
   <!-- 可以自定义后缀实现请求
   	注意：*前面不能加项目映射的路径
   -->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>*.study</url-pattern>
   </servlet-mapping>
   ```

5. 指定了固有的映射路径优先级最高，如果找不到就会走默认的处理请求

   ```xml
   <!-- 注册Servlet -->
   <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>com.study.servlet.HelloServlet</servlet-class>
   </servlet>
   
   <!-- Servlet的请求路径 -->
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
   </servlet-mapping>
   
   <servlet>
       <servlet-name>error</servlet-name>
       <servlet-class>com.study.servlet.ErrorServlet</servlet-class>
   </servlet>
   <servlet-mapping>
       <servlet-name>error</servlet-name>
       <url-pattern>/*</url-pattern>
   </servlet-mapping>
   ```




## 1.5、ServletContext

web容器在启动的时候，它会为每个web程序都创建一个对应的ServletContext对象，它代表了当前的web应用。

### 1、共享数据

在这个Servlet中保存的数据，可以在另一个Servlet中拿到

![1635858050159](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635858050159.png)

```java
/**
	放置数据的类
 */
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // this.getInitParameter();  初始化参数
        // this.getServletConfig();  Servlet配置
        // this.getServletContext(); Servlet上下文
        ServletContext context = this.getServletContext();

        String username = "用户"; // 数据
        context.setAttribute("username", username); // 将一个数据保存在了ServletContext中，名字为username，值为username
    }
}
```

```java
/**
	读取数据的类
 */
public class GetServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        String username = (String) context.getAttribute("username");

        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        resp.getWriter().print("名字" + username);
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee     http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.study.servlet.HelloServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>getc</servlet-name>
        <servlet-class>com.study.servlet.GetServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>getc</servlet-name>
        <url-pattern>/getc</url-pattern>
    </servlet-mapping>

</web-app>
```

测试访问结果



### 2、获取初始化参数

```xml
<!-- 在web.xml中配置 -->
<!-- 配置一些web应用的初始化参数 -->
<context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql://localhost:3306/mybatis</param-value>
</context-param>
```

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    ServletContext context = this.getServletContext();
    String url = context.getInitParameter("url");
    resp.getWriter().print(url);
}
```



### 3、请求转发

![1635859981960](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635859981960.png)

转发后路径不会发生改变

![1635860178500](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635860178500.png)

A只请求了B，从始至终都没有直接和C产生关系



重定向中发生了实质性的变化

![1635860310787](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635860310787.png)



```java
// 转发
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    ServletContext context = this.getServletContext();
    RequestDispatcher requestDispatcher = context.getRequestDispatcher("/gp"); // 设置转发的路径
    requestDispatcher.forward(req, resp);// 调用forward实现请求转发
}
```



### 4、读取资源文件

![1635861179126](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/1635861179126.png)

将文件放至resources目录下，打包后会和com代码包在classes目录下生成。

这个classes目录被称为classpath，也就是类路径

如果把配置文件放在了其他目录，比如com.study.servlet目录下，打包时就不会被整合进包，需要在pom文件中配置静态资源导出。这样就可以把配置文件打入com.study.servlet中了



```java
// 使用Properties对象读取配置文件
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    InputStream is = this.getServletContext().getResourceAsStream("/WEB-INF/classes/db.properties");
    Properties prop = new Properties();
    prop.load(is);
    String username = prop.getProperty("username");
    String password = prop.getProperty("password");

    resp.getWriter().print(username + ": " + password);
}
```

关于代码中的`/WEB-INF/classes/db.properties`：

1. 第一个`/`代表打包后的`servlet-02-1.0-SNAPSHOT`，代表当前web程序根目录
2. ``/WEB-INF/classes`是打包后的class文件和配置文件的文件夹



## 1.6、HttpServletResponse

web服务器接收到客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest对象，代表响应的HttpServletResponse对象。

- 如果要获取客户端请求过来的参数：找HttpServletRequest
- 如果要给客户端响应一些信息：找HttpServletResponse

### 1、简单分类

HttpServletResponse接口中的方法分类

负责向浏览器发送数据的方法

```java
ServletOutputStream getOutputStream() throws IOException;
PrintWriter getWriter() throws IOException;
```

负责向浏览器发送响应头的方法

```java
void setCharacterEncoding(String var1);
void setContentLength(int var1);
void setContentLengthLong(long var1);
void setContentType(String var1);
void setDateHeader(String var1, long var2);
void addDateHeader(String var1, long var2);
void setHeader(String var1, String var2);
void addHeader(String var1, String var2);
void setIntHeader(String var1, int var2);
void addIntHeader(String var1, int var2);
```

响应的状态码常量(此处不再列举)

### 2、下载文件

1. 向浏览器输出消息

2. 下载文件

   1. 要获取下载文件的路径
   2. 下载的文件名是什么
   3. 想办法设置让浏览器能够支持下载我们需要的东西
   4. 获取下载文件的输入流
   5. 创建缓冲区
   6. 获取OutputStream对象
   7. 将FileOutputStream流写入到buffer缓冲区
   8. 使用OutputStream将缓冲区中的数据输出到客户端

   ```java
   protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       // 1. 要获取下载文件的路径
       String realPath = "D:\\study-code\\javaweb\\javaweb-02-servlet\\response\\src\\main\\resources\\1.jpg";
       System.out.println("下载文件的路径：" + realPath);
       // 2. 下载的文件名是什么
       String fileName = realPath.substring(realPath.lastIndexOf("\\") + 1);
       // 3. 想办法设置让浏览器能够支持下载我们需要的东西   中文文件名用URLEncoder.encode编码，否则文件名可能乱码
       resp.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName, "utf-8"));
       // 4. 获取下载文件的输入流
       FileInputStream fis = new FileInputStream(realPath);
       // 5. 创建缓冲区
       int len = 0;
       byte[] buffer = new byte[1024];
       // 6. 获取OutputStream对象
       ServletOutputStream os = resp.getOutputStream();
       // 7. 将FileOutputStream流写入到buffer缓冲区
       while ((len = fis.read(buffer)) != -1) {
           // 8. 使用OutputStream将缓冲区中的数据输出到客户端
           os.write(buffer, 0 ,len);
       }
       fis.close();
       os.close();
   }
   ```

### 3、验证码功能

验证怎么来的

- 前端实现
- 后端实现，需要用到java的图片类，生成一个图片

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 如何让浏览器五秒自动刷新一次
    resp.setHeader("refresh", "3");
    // 在内存中创建一个图片
    BufferedImage image = new BufferedImage(80, 20, BufferedImage.TYPE_INT_RGB);
    // 得到图片
    Graphics2D g = (Graphics2D) image.getGraphics(); // 画笔
    // 设置图片的背景颜色
    g.setColor(Color.WHITE);
    g.fillRect(0, 0, 80, 20);
    // 给图片写数据
    g.setColor(Color.BLUE);
    g.setFont(new Font(null, Font.BOLD, 20));
    g.drawString(makeNum(), 0, 20);
    // 告诉浏览器这个请求用图片的方式打开
    resp.setContentType("image/png");
    // 网站存在缓存，不让浏览器缓存
    resp.setDateHeader("expirse", -1);
    resp.setHeader("Cache-Control", "no-cache");
    resp.setHeader("Pragma", "no-cache");
    // 把图片写给浏览器
    boolean write = ImageIO.write(image, "png", resp.getOutputStream());
}

// 生成随机数
private String makeNum() {
    Random random = new Random();
    String num = random.nextInt(9999999) + "";
    StringBuffer sb = new StringBuffer();
    for (int i = 0; i < 7 - num.length(); i++) {
        sb.append("0");
    }
    return sb.toString() + num;
}
```

### 4、重定向

![image-20220712210846441](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220712210846441.png)

一个web资源收到客户端请求后，它会通知客户端去访问另外一个web资源，这个过程就叫重定向。

常见场景：

- 用户登录

  ```java
  void sendRedirect(String var1) throws IOException;
  ```

  测试：

  ```java
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      /**
       * 根据 F12 查看重定向的底层原理
       * resp.setHeader("Location", "/r/img");
       * resp.setStatus(302);
       */
      resp.sendRedirect("/r/img");
  }
  ```
  

面试题：重定向和转发的区别？

相同点：页面都会跳转

不同点：请求转发的时候，url不会产生变化；重定向的时候，url地址栏会发生变化。

### 5、使用重定向实现登录

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 处理请求
    String username = req.getParameter("username");
    String password = req.getParameter("password");
    System.out.println(username + "：" + password);
    // 重定向的时候一定要注意路径问题，否则就会404
    resp.sendRedirect("/r/success.jsp");
}
```



## 1.7、HttpServletRequest

HttpServletRequest代表客户端的请求，用户通过Http协议访问服务器，Http请求中的所有信息会被封装到HttpServletRequest，通过这个HttpServletRequest的方法，获得客户端的所有信息。

### 1、获取前端传递的参数

![image-20220712214637653](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220712214637653.png)

### 2、请求转发

```java
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.setCharacterEncoding("UTF-8");
    resp.setCharacterEncoding("UTF-8");
    String username = req.getParameter("username");
    String password = req.getParameter("password");
    String[] hobbys = req.getParameterValues("hobbys");
    System.out.println("=========================================");
    // 后台接收中文乱码问题
    System.out.println(username);
    System.out.println(password);
    System.out.println(Arrays.toString(hobbys));
    System.out.println("=========================================");
    // 这里的 / 代表当前的web应用
    req.getRequestDispatcher("/success.jsp").forward(req, resp);
}
```



# 二、Cookie、Session

## 2.1、会话

会话：用户打开一个浏览器，点击了很多超链接，访问多个web资源，关闭浏览器，这个过程可以称之为会话。

有状态会话：一个同学来过教室，下次再来教室，我们会知道这个同学曾经来过，称为有状态会话。

**一个网站，怎么证明用户来过？**

客户端    服务端

1. 服务端给客户端一个信件，客户端下次访问服务端带上信件就可以了；cookie
2. 服务器登记用户来过了，下次用户来的时候服务器来匹配用户；session



## 2.2、保存会话的两种技术

cookie：客户端技术(响应、请求)

session：服务器技术，利用这个技术，可以保存用户的会话信息。可以把信息或数据放在session中

常见应用：网站登录后，下次就不需要再登陆了，第二次访问直接就上去了



## 2.3、Cookie

1. 从请求中拿到cookie信息
2. 服务器响应给客户端cookie

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 保存用户的上一次请求时间
    // 服务器告诉客户端来的时间，把这个时间封装成为一个信件，下次请求时客户端带着信件，服务器就知道是用户来了
    // 解决中文乱码
    req.setCharacterEncoding("utf-8");
    resp.setCharacterEncoding("gbk");
    PrintWriter out = resp.getWriter();
    // Cookie，服务器端从客户端获取
    Cookie[] cookies = req.getCookies(); // 这里返回的数组，说明cookie可能存在多个
    // 判断cookie是否存在
    if (cookies != null) {
        // 如果存在
        out.write("你上一次访问的时间是：");
        for (int i = 0; i < cookies.length; i++) {
            Cookie cookie = cookies[i];
            // 获取cookie的名字
            if (cookie.getName().equals("lastLoginTime")) {
                // 获取cookie的值
                long lastLoginTime = Long.parseLong(cookie.getValue());
                Date date = new Date(lastLoginTime);
                out.write(date.toLocaleString());
            }
        }
    }else {
        out.write("这是您第一访问本站");
    }
    // 服务器给客户端响应一个cookie
    Cookie cookie = new Cookie("lastLoginTime", System.currentTimeMillis() + "");
    // 设置cookie过期时间
    cookie.setMaxAge(24*60*60);
    resp.addCookie(cookie);
}
```

服务器响应cookie后，在响应头中可以看到Set-Cookie，再次请求会在请求头中看到Cookie。重启浏览器后，Cookie消失。

如果设置了Cookie的过期时间，重启浏览器后，Cookie也存在。

![image-20220713214513400](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220713214513400.png)



**cookie：一般会保存在本地 C盘的用户目录下 appdata 中**

**一个网站cookie是否存在上限？**

- 一个cookie只能保存一个信息
- 一个web站点可以给浏览器发送多个cookie，最多存放20个cookie
- cookie大小有限制，4KB
- 浏览器的上限是300个cookie



**删除cookie：**

- 不设置有效期，关闭浏览器，自动失效
- 设置有效期时间为0



**编码解码：**

```java
URLEncoder.encode("测试用例", "utf-8")
URLDecoder.decode(cookie.getValue(), "utf-8")
```

![image-20220717175658670](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220717175658670.png)



## 2.4、Session

什么是Session？

- 服务器会给每一个用户(浏览器)创建一个Session对象
- 一个Session独占一个浏览器，只要浏览器没关，这个Session就一直在
- 用户登录后，整个网站它都可以访问。--->保存用户的信息；保存购物车的信息



Session和Cookie的区别：

- Cookie是把用户的数据写给用户的浏览器，浏览器保存(可以保存多个)
- Session把用户的数据写到用户独占的Session中，服务器端保存(保存重要的信息，减少服务器资源的浪费)
- Session对象由服务器创建
- Session只能保存单个用户的信息，ServletContext可以保存全局的，所有用户都可以访问。



使用场景：

- 保存一个登录用户的信息
- 购物车信息
- 在整个网站中经常会使用的数据，都保存在Session中



Session的使用：

```java
// 存入数据
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 解决乱码问题
    req.setCharacterEncoding("utf-8");
    resp.setCharacterEncoding("utf-8");
    resp.setContentType("text/html;charset=utf-8");
    // 得到Session
    HttpSession session = req.getSession();
    // 给Session中存东西
    session.setAttribute("name", "测试");
    session.setAttribute("person", new Person("测试", 25));
    // 获取Session的id
    String id = session.getId();
    // 判断Session是不是新创建的
    if (session.isNew()) {
        resp.getWriter().write("session 创建成功，id为：" + id);
    }else {
        resp.getWriter().write("session 已经在服务器中存在了，id为：" + id);
    }
    // Session创建时做了什么事情
    // Cookie cookie = new Cookie("JESESSION", id);
    // resp.addCookie(cookie);
}
// 取出数据
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 解决乱码问题
    req.setCharacterEncoding("utf-8");
    resp.setCharacterEncoding("utf-8");
    resp.setContentType("text/html;charset=utf-8");
    // 得到Session
    HttpSession session = req.getSession();
    Object name = session.getAttribute("name");
    System.out.println((String) name);
    Object person = session.getAttribute("person");
    System.out.println(person.toString());
    // 移除数据
    session.removeAttribute("name");
    // 手动注销Session，一旦注销，就会立即自动生成一个新的
    session.invalidate();
}
```

```xml
<!-- 设置Session的默认失效时间 -->
<session-config>
    <!-- 1分钟后，Session自动失效，以分钟为单位 -->
    <session-timeout>1</session-timeout>
</session-config>
```



![image-20220717180050898](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220717180050898.png)



