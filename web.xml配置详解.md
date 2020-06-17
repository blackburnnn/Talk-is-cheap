
[史上最全web.xml配置文件元素详解cnblogs](https://www.cnblogs.com/hafiz/p/5715523.html)

- [web.xml配置文件常用元素及其意义预览](#webxml配置文件常用元素及其意义预览)

- [各个配置元素详解](#各个配置元素详解)

三、详解web.xml中元素的加载顺序

# web.xml配置文件常用元素及其意义预览

```xml
<web-app>
     <!--定义了WEB应用的名字-->
     <display-name></display-name>
     <!--声明WEB应用的描述信息-->
     <description></description>
     <!--context-param元素声明应用范围内的初始化参数-->
     <context-param></context-param>
     <!--过滤器元素将一个名字与一个实现javax.servlet.Filter接口的类相关联-->
     <filter></filter>
     <!--一旦命名了一个过滤器，就要利用filter-mapping元素把它与一个或多个servlet或JSP页面相关联-->
     <filter-mapping></filter-mapping>
     <!--servlet API的版本2.3增加了对事件监听程序的支持，事件监听程序在建立、修改和删除会话或servlet环境时得到通知。
         Listener元素指出事件监听程序类-->
     <listener></listener>
     <!--在向servlet或JSP页面制定初始化参数或定制URL时，必须首先命名servlet或JSP页面。
         Servlet元素就是用来完成此项任务的-->
     <servlet></servlet>
     <!--服务器一般为servlet提供一个缺省的URL：http://host/webAppPrefix/servlet/ServletName。
         但是，常常会更改这个URL，以便servlet可以访问初始化参数或更容易地处理相对URL。
         在更改缺省URL时，使用servlet-mapping元素-->
     <servlet-mapping></servlet-mapping>
     <!--如果某个会话在一定时间内未被访问，服务器可以抛弃它以节省内存。可通过使用HttpSession的
         setMaxInactiveInterval方法明确设置单个会话对象的超时值，或者可利用session-config元素制定缺省超时值-->
     <session-config></session-config>
     <!--如果Web应用具有想到特殊的文件，希望能保证给他们分配特定的MIME类型，则mime-mapping元素提供这种保证-->
     <mime-mapping></mime-mapping>
     <!--指示服务器在收到引用一个目录名而不是文件名的URL时，使用哪个文件-->
     <welcome-file-list></welcome-file-list>
     <!--在返回特定HTTP状态代码时，或者特定类型的异常被抛出时，能够制定将要显示的页面-->
     <error-page></error-page>
     <!--对标记库描述符文件（Tag Libraryu Descriptor file）指定别名。此功能使你能够更改TLD文件的位置，
         而不用编辑使用这些文件的JSP页面-->
     <taglib></taglib>
     <!--声明与资源相关的一个管理对象-->
     <resource-env-ref></resource-env-ref>
     <!--声明一个资源工厂使用的外部资源-->
     <resource-ref></resource-ref>
     <!--制定应该保护的URL。它与login-config元素联合使用-->
     <security-constraint></security-constraint>
     <!--指定服务器应该怎样给试图访问受保护页面的用户授权。它与sercurity-constraint元素联合使用-->
     <login-config></login-config>
     <!--给出安全角色的一个列表，这些角色将出现在servlet元素内的security-role-ref元素的role-name子元素中。
         分别地声明角色可使高级IDE处理安全信息更为容易-->
     <security-role></security-role>
     <!--声明Web应用的环境项-->
     <env-entry></env-entry>
     <!--声明一个EJB的主目录的引用-->
     <ejb-ref></ejb-ref>
     <!--声明一个EJB的本地主目录的应用-->
     <ejb-local-ref></ejb-local-ref>
 </web-app> 
 ```

# 各个配置元素详解
1.Web应用图标：指出IDE和GUI工具用来表示Web应用的大图标和小图标
```xml
<icon>  
      <small-icon>/images/app_small.gif</small-icon>  
      <large-icon>/images/app_large.gif</large-icon>  
 </icon>
```

2.Web 应用名称：提供GUI工具可能会用来标记这个特定的Web应用的一个名称
```xml
<display-name>Tomcat Example</display-name>
```

3.Web 应用描述：给出于此相关的说明性文本
```xml
<desciption>Tomcat Example servlets and JSP pages.</desciption>
```
4.上下文参数：声明应用范围内的初始化参数
```xml
<context-param>
     <param-name>参数名</para-name>
     <param-value>参数值</param-value>
     <description>参数描述</description>
 </context-param>
```

在servlet里面可以通过 getServletContext().getInitParameter(“context/param”)得到

5.过滤器配置：将一个名字与一个实现javaxs.servlet.Filter接口的类相关联
```xml
<filte>
    <filter-name>setCharacterEncoding</filter-name>
    <filter-class>com.myTest.setCharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>GB2312</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>setCharacterEncoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
6.监听器配置
```xml
<listener>
     <listerner-class>org.springframework.web.context.ContextLoaderListener</listener-class>
 </listener>
```
7.Servlet配置
```xml
<servlet>
    <servlet-name>servlet名称</servlet-name>
    <servlet-class>servlet类全路径</servlet-class>
    <init-param>
        <param-name>参数名</param-name>
        <param-value>参数值</param-value>
    </init-param>
    <run-as>
        <description>Security role for anonymous access</description>
        <role-name>tomcat</role-name>
    </run-as>
    <load-on-startup>指定当Web应用启动时，装载Servlet的次序</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>servlet名称</servlet-name>
    <url-pattern>映射路径</url-pattern>
</servlet-mapping>
```

8.会话超时配置（单位为分钟）
```xml
 <session-config>
     <session-timeout>120</session-timeout>
 </session-config>
```
9.MIME类型配置
```
<mime-mapping>
     <extension>htm</extension>
     <mime-type>text/html</mime-type>
 </mime-mapping>
```

10.指定欢迎文件页配置
```
<welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
</welcome-file-list>
```
11.配置错误页面
(1).通过错误码来配置error-page
```xml
<!--配置了当系统发生404错误时，跳转到错误处理页面NotFound.jsp-->
<error-page>
    <error-code>404</error-code>
    <location>/NotFound.jsp</location>
</error-page>
```
(2).通过异常的类型配置error-page
```xml
<!--配置了当系统发生java.lang.NullException（即空指针异常）时，跳转到错误处理页面error.jsp-->
<error-page>
    <exception-type>java.lang.NullException</exception-type>
    <location>/error.jsp</location>
</error-page>
```
12.TLD配置
```xml
<taglib>
    <taglib-uri>http://jakarta.apache.org/tomcat/debug-taglib</taglib-uri>
    <taglib-location>/WEB-INF/jsp/debug-taglib.tld</taglib-location>
</taglib>
```

如果开发工具一直在报错,应该把<taglib> 放到 <jsp-config>中
```
<jsp-config>
    <taglib>
        <taglib-uri>http://jakarta.apache.org/tomcat/debug-taglib</taglib-uri>
        <taglib-location>/WEB-INF/pager-taglib.tld</taglib-location>
    </taglib>
</jsp-config>
```
13.资源管理对象配置
```
<resource-env-ref>
    <resource-env-ref-name>jms/StockQueue</resource-env-ref-name>
 </resource-env-ref>
```
14.资源工厂配置
```xml
<resource-ref>
    <res-ref-name>mail/Session</res-ref-name>
    <res-type>javax.mail.Session</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
```
配置数据库连接池就可在此配置
```xml
<resource-ref>
    <description>JNDI JDBC DataSource of shop</description>
    <res-ref-name>jdbc/sample_db</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
```
15.安全限制配置
```xml
<security-constraint>
      <display-name>Example Security Constraint</display-name>
      <web-resource-collection>
          <web-resource-name>Protected Area</web-resource-name>
          <url-pattern>/jsp/security/protected/*</url-pattern>
          <http-method>DELETE</http-method>
          <http-method>GET</http-method>
          <http-method>POST</http-method>
          <http-method>PUT</http-method>
      </web-resource-collection>
      <auth-constraint>
          <role-name>tomcat</role-name>
          <role-name>role1</role-name>
      </auth-constraint>
 </security-constraint>
```
16.登陆验证配置
```xml
<login-config>
    <auth-method>FORM</auth-method>
    <realm-name>Example-Based Authentiation Area</realm-name>
    <form-login-config>
        <form-login-page>/jsp/security/protected/login.jsp</form-login-page>
        <form-error-page>/jsp/security/protected/error.jsp</form-error-page>
    </form-login-config>
</login-config>
```
17.安全角色：security-role元素给出安全角色的一个列表，这些角色将出现在servlet元素内的security-role-ref元素的role-name子元素中。
分别地声明角色可使高级IDE处理安全信息更为容易。
```xml
<security-role>
    <role-name>tomcat</role-name>
</security-role>
```
18.Web环境参数：env-entry元素声明Web应用的环境项
```xml
<env-entry>
    <env-entry-name>minExemptions</env-entry-name>
    <env-entry-value>1</env-entry-value>
    <env-entry-type>java.lang.Integer</env-entry-type>
</env-entry>
```
19.EJB 声明
```xml
<ejb-ref>
    <description>Example EJB reference</decription>
    <ejb-ref-name>ejb/Account</ejb-ref-name>
    <ejb-ref-type>Entity</ejb-ref-type>
    <home>com.mycompany.mypackage.AccountHome</home>
    <remote>com.mycompany.mypackage.Account</remote>
</ejb-ref>
```
20.本地EJB声明
```xml
<ejb-local-ref>
    <description>Example Loacal EJB reference</decription>
    <ejb-ref-name>ejb/ProcessOrder</ejb-ref-name>
    <ejb-ref-type>Session</ejb-ref-type>
    <local-home>com.mycompany.mypackage.ProcessOrderHome</local-home>
    <local>com.mycompany.mypackage.ProcessOrder</local>
</ejb-local-ref>
```
以上就是常用的web.xml中元素的配置以及作用

# 详解web.xml中元素的加载顺序
背景：
最近在项目中遇到了启动时出现加载service注解注入失败的问题，后来经过不懈努力发现了是因为web.xml配置文件中的元素加载顺序导致的，那么就抽空研究了以下tomcat在启动时web.xml文件中元素的加载顺序。

问题剖析和研究结果
遇到这种问题的时候，一般看源码是最直接和最权威的获取答案的方式，根据tomcat架构设计Context的实现类是StandardContext，全称org.apache.catalina.core.StandardContext。看到其实现Lifecycle接口，我们在StandardContext中找到startInternal方法，下面给出我把暂时无用的代码去掉后的注释版源码：
```java
/**
 * Start this component and implement the requirements
 * of {@link org.apache.catalina.util.LifecycleBase#startInternal()}.
 *
 * @exception LifecycleException if this component detects a fatal error
 *  that prevents this component from being used
 */
 @Override
 protectedsynchronized void startInternal() throwsLifecycleException {
  //设置webappLoader 代码省略
  
  // Standard container startup 代码省略
  
 　　try{
  
 　　　　// Set up the context init params 
 　　　　//初始化context-param节点数据
 　　　　mergeParameters();
  
 　　　　// Configure and call application event listeners
 　　　　//配置和调用应用程序事件listeners 
 　　　　if(ok) {
 　　　　　　if(!listenerStart()) {
 　　　　　　　　log.error("Error listenerStart");
 　　　　　　　　ok = false;
 　　　　　　}
 　　　　}
  
 　　　　// Configure and call application filters
 　　　　//配置和调用应用程序filters
 　　　　if(ok) {
 　　　　　　if(!filterStart()) {
 　　　　　　　　log.error("Error filterStart");
 　　　　　　　　ok = false;
 　　　　　　}
 　　　　}
  
 　　　　// Load and initialize all "load on startup" servlets
 　　　　//加载和初始化配置在load on startup的servlets
 　　　　if(ok) {
 　　　　　　loadOnStartup(findChildren());
 　　　　}
  
 　　　　// Start ContainerBackgroundProcessor thread
 　　　　super.threadStart();
 　　}finally{
 　　　　// Unbinding thread
 　　　　unbindThread(oldCCL);
 　　}
 }
```
那我们接着归纳和整理一下代码：
1. 首先初始化context-param节点
2. 接着配置和调用listeners 并开始监听
3. 然后配置和调用filters filters开始起作用
4. 最后加载和初始化配置在load on startup的servlets
即元素加载顺序为：
```
context-param --> listeners --> filters --> servlets
```
注意：
1. 该加载顺序并不会受元素在web.xml文件中的位置的影响。
2. 但对于某类配置节而言，与它们出现的顺序是有关的。以 filter 为例，web.xml 中当然可以定义多个 filter，与 filter 相关的一个配置节是 filter-mapping，这里一定要注意，对于拥有相同 filter-name 的 filter 和 filter-mapping 配置节而言，filter-mapping 必须出现在 filter 之后，否则当解析到 filter-mapping 时，它所对应的 filter-name 还未定义。web 容器启动时初始化每个 filter 时，是按照 filter 配置节出现的顺序来初始化的，当请求资源匹配多个 filter-mapping 时，filter 拦截资源是按照 filter-mapping 配置节出现的顺序来依次调用 doFilter() 方法的。

#### 接着让我们来回忆一下web项目的启动顺序:

1. web容器读取web.xml配置文件，并首先读取<context-param>和<listener>两个结点。
2. 容器创建一个ServletContext（servlet上下文），该web项目的所有部分都将共享这个上下文。
3. 容器将<context-param>转换为键值对，并交给servletContext。
4. 容器按照load on startup中的启动顺序创建<listener>中的类实例，创建监听器。
关于load on startup
　　load-on-startup 元素在web应用启动的时候指定了servlet被加载的顺序，它的值必须是一个整数。
　　如果它的值是一个负整数或是这个元素不存在，那么容器会在该servlet被调用的时候，加载这个servlet 。
　　如果值是正整数或零，容器在配置的时候就加载并初始化这个servlet，容器必须保证值小的先被加载。如果值相等，容器可以自动选择先加载谁。
　　正数的值越小，启动该servlet的优先级越高。