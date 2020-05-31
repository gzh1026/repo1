**spring** **mvc**

springmvc的思想是有一个前端控制器能拦截所有请求,并智能派发,前端控制器是一个servlet,应该在web.xml中配置这个wervlet来拦截所有的请求.

```xml
  <!--配置前端控制器-->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
        指定springmvc配置文件的位置
    </init-param>
    <load-on-startup>1</load-on-startup>
      服务器启动的时候创建对象,值越小,优先级越高,越先创建对象
  </servlet>


  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
      /代表拦截所有请求   但是/*还会拦截所有的jsp页面
  </servlet-mapping>
```

接下来配置springmvc的配置文件springmvc.xml

```xml
扫描所有组件
    <!-- 开启注解扫描 -->
    <context:component-scan base-package="cn.itcast"/>
    <!-- 视图解析器对象 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
```

然后创建一个类,controller,添加@controller注解

```java
@controller
Public class myfirstcontroller{
	@requestmapping("/hello")  //请求映射,/代表从当前项目下开始处理当前项目
	public string myfirstrequest(){
		return"success"
	}
}
```

**细节**

1. 运行流程

   客户端点击链接会发送http://hocalhost:8080/springmvc/hello请求

   来到tomcat服务器

   springmvc前端控制器收到所有亲求

   来看请求地址与@requestmapping标注的那个匹配,找到到底是用哪个类的哪个方法.

   前端控制器找到了目标处理器和目标方法,直接利用返回执行目标

   方法执行完之后会有一个返回值,springmvc认为这个返回值就是要去的地方

   拿到方法返回值以后,用试图解析器进行拼串,得到完整的页面地址

   拿到页面地址,前端控制器帮我们转发到页面

2. @requestmapping

   告诉springmvc,这个方法用来处理什么请求

   这个/可以省略,即使省略,也是默认从当前项目下开始

   习惯加上比较好  /hello

3. 如果不指定配置文件位置

   如果不指定文件位置,则会默认去一个文件里`WEB-INF/springdispatcherservlet-servlet.xml`

   