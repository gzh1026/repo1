## spring入门理解

maven导入一些依赖，也就是设置pom.xml文件

```xml
    <groupId>junit</groupId>
    <groupId>dom4j</groupId>
    <groupId>org.projectlombok</groupId>
    <groupId>antlr</groupId>
    <groupId>aopalliance</groupId>
    <groupId>org.aspectj</groupId>
    <groupId>commons-collections</groupId>
    <groupId>commons-logging</groupId>
    <groupId>commons-logging</groupId>
    <groupId>dom4j</groupId>
 ```
      
然后设置两个类，Category和Product
```java
public class Category implements Serializable {
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    private int id;
    private String name;
}


public class Product implements Serializable {
    private int id;
    private String name;
    private Category category;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Category getCategory() {
        return category;
    }
    public void setCategory(Category category) {
        this.category = category;
    }
}

```
再设置applicationContext.xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-3.0.xsd">
<!--    <context:annotation-config></context:annotation-config>-->
    <bean name="c" class="com.how2java.pojo.Category">
        <property name="name" value="category 1" />
    </bean>
    <bean name="p" class="com.how2java.pojo.Product">
        <!--ref可以导入其他的bean-->
        <property name="name" value="product1" />
        <property name="category" ref="c" />
    </bean>
</beans>
```
最后设置一个test的类进行测试spring的注入DI
```java
public class MyTest {

    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
//        Category c = (Category) context.getBean("c");
        Category c=context.getBean("c",Category.class);
//        System.out.println(c.getName());

//        Product p = (Product) context.getBean("p");
        Product p = context.getBean("p", Product.class);
        System.out.println(p.getName());
        System.out.println(p.getCategory().getName());
    }
}
```

* 解释：
    * 当test运行的时候，ApplicationContext获取容器，c得到bean，真正的注入是在配置的applicationcontext.xml文件中，使用bean标签中的name属性对com.how2java.pojo.Category这个路径下的类进行重命名为c，其中Category中的name被赋值为category 1，在第二个bean中，```<property name="category" ref="c" />```是指将c，也就是Category这个类引入其中命名为category，当执行```p.getCategory().getName()```时，p也就是```com.how2java.pojo.Product```这个类，先getCategory，获得getCategory这个方法的返回值category，进而，由于在```<property name="category" ref="c" />```已经将ref所指的对象c->category中name进行赋值，所以，```p.getCategory().getName()```的返回结果是category  1