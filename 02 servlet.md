##   servlet : server applet
* 概念:运行在服务器端的小程序
    * servlet就是一个接口，定义了java类被浏览器访问到(tomcat识别)的规则 
    * 将来我们自定义一个类，实现servlet接口，复写方法。
* 快速入门：
    1. 创建java ee项目
    2. 定义一个类，实现servlet接口。
       
        * ` public class ServletDemo1 implements Servlet `
    3. 实现接口中的抽象方法
    4. 配置servlet
        ```xml
            <!--    在web.xml中配置servlet-->
            <!-- 中间曾出现了一个http500的错误，原因是误把servletdemo1文件放到了其他目录下，而不是JSP项目的src目录下 -->
        <servlet>
            <servlet-name>demo1</servlet-name>
            <servlet-class>ServletDemo1</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>demo1</servlet-name>
            <url-pattern>/demo1</url-pattern>
        </servlet-mapping>
        ```
* 执行原理 ：
    1. 当服务器接收到客户端浏览器的请求后，会解析请求url路径，获取访问的servlet的资源路径
    2. 查找web.xml文件，是否有对应的<url-pattern>标签及内容
    3. 如果有，则找到对应的<servlet-class>全类名
    4. tomacat会将字节码文件加载进内存，并且创建其实例对象
    5. 实例创建过程中，自动调用其方法
* servlet的生命周期
    1. 被创建：执行init，只执行一次
        * servlet什么时候被创建
            * 默认情况下，第一次被访问时创建
            * 可以配置执行servlet的创建时机
                ```xml
                <!--指定servlet的创建时机(在servlet标签下创建)
                1. 第一次被访问时创建。load-on-startup的值为负数
                2. 在服务器启动时，创建。load-on-startup的值为0或正-->
                    <load-on-startup>5</load-on-startup>
                ```
        * servlet的init方法，只执行一次，说明servlet在内存中只存在一个对象，servlet是单例
            * 多个用户同时访问的时候，可能存在线程安全问题
            * 解决：尽量不要在servlet中定义成员变量。即使定义了，也不要对其修改值。
    2. 提供服务：执行service，执行多次
       
        * 每次访问servlet时，service方法都会被执行一次
    3. 被销毁：执行destroy，只执行一次 
        * servlet被销毁时执行，服务器关闭时，servlet被销毁。
        * 只有服务器被正常关闭时，才会执行destroy方法。
        * destroy方法在servlet被销毁前执行，一般用于释放资源。
* servlet3.0：
    * 好处支持注解配置。可以不需要web.xml。
    * 步骤：
        1. 创建javaee项目，选择servlet3.0版本以上，可以不创建web.xml
        2. 定义一个类，实现servlet接口
        3. 复写方法
        4. 在类上使用@webservlet注解，进行配置。
            ```java
                @WebServlet("资源路径")
                public class ServletDemo implements Servlet{}

                @Target({ElementType.TYPE})
                @Retention(RetentionPolicy.RUNTIME)
                @Documented
                public @interface WebServlet {
                String name() default "";
                String[] value() default {};
                String[] urlPatterns() default {};
                int loadOnStartup() default -1;
                WebInitParam[] initParams() default {};
                boolean asyncSupported() default false;
                String smallIcon() default "";
                String largeIcon() default "";
                String description() default "";
                String displayName() default "";}
            ```
## IDEA与tomcat的相关配置
1. idea会为每一个tomcat部署的项目单独建立一份配置文件   
    * 查看控制台的输出 Using CATALINA_BASE:  C:\Users\gzh\ .IntelliJIdea2019.3\system\tomcat\ _demoo
2. 工作空间项目和tomcat部署的web项目
    * tomcat真正访问的是“tomcat部署的web项目”，“tomcat部署的文本项目”对应着“工作空间项目”的web目录下的所有资源
    * web-info下的文件不能被直接访问。
    * 断点调试