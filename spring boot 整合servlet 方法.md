## spring boot 整合servlet 方法

### 方法一

通过注解扫描完成servlet组件的注册,创建servlet

```java
@WebServlet(name = "firstservlet",urlPatterns = "/first")
public class helloservlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        System.out.println("servlet ...");
    }
}

```

修改启动类,添加`@servletcomponnetscan`注解来扫描`@webservlet`注解的类,并将该类实例化.

```java
@SpringBootApplication
@ServletComponentScan
public class SpringbootQApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootQApplication.class, args);
    }
}

```

### 方式二

通过方法完成servlet组件的注册,创建servlet,然后创建servlet配置类

```java
public class hiservlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("hi servlet...");
    }
}
```

1. 可以创建一个配置类,完成servlet组件的注册

```java
@Configuration
public class config {
    @Bean
    public RegistrationBean getregistrationbean(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new hiservlet());
        bean.addUrlMappings("/second");
        return bean;
    }
}
```

2. 将此方法写进`@Configuration`中

**相比于方法一,方法二中,每注册一个servlet方法,就需要添加一个配置类,更为推荐方法一**.