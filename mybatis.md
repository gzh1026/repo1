## mybatis
* 介绍：是一个持久性框架，java编写，封装了很多jdbc操作的很多细节，使开发者只需要关注sql语句本身，而无需关注注册驱动，创建连接等繁杂过程，它使用了ORM思想实现了结果集的封装。
    * ORM：object relational mapping对象关系映射，就是把数据库表及实体类的属性对应起来，让我们可以操作实体类就可以操作数据库表。
    * 实体类中的属性和数据库表的字段名称保持一致。
* mybatis入门：
    * mybatis的环境搭建
        * 第一步：创建maven工程并导入xml
            ```xml
             <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.2.8</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.48</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.17</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
            </dependency>
            ```
        * 第二步：创建实体类和```dao```的接口
            * 实体类我们设置的是```User(Integer id,string username,date birthday,string sex,string address)```
            并且我们使用```public class User implements Serializable```通过引入serializable进行对象序列化
            * 接口：
                ```java
                public interface IUserDao {
                    //查询所有操作
                    List<User> findAll();
                }
                ```
        * 第三步：创建mybatis的主配置文件：sqlmapconfig.xml
            ```xml
            <?xml version="1.0" encoding="UTF-8"?>
            <!DOCTYPE configuration
                    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                    "http://mybatis.org/dtd/mybatis-3-config.dtd">
            <!--mybatis主配置文件-->
            <configuration>
                <!--配置环境-->
                <environments default="mysql">
                    <!--配置MySQL环境-->
                    <environment id="mysql">
                        <!--配置事务类型-->
                        <transactionManager type="JDBC"></transactionManager>
                        <!--配置数据源（连接池）-->
                        <dataSource type="POOLED">
                            <!--配置连接数据库的基本信息,sql映射文件注册在全局配置文件中-->
                            <property name="driver" value="com.mysql.jdbc.Driver"/>
                            <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"/>
                            <property name="username" value="root"/>
                            <property name="password" value="admin"/>
                        </dataSource>
                    </environment>
                </environments>
                <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
                <mappers>
                    <mapper resource="com/itheima/dao/IUserDao.xml"/>
                </mappers>
            </configuration>
            ```
        * 第四步：创建映射配置文件：IUDao.xml ，其在resources目录下。
            ```xml
            <?xml version="1.0" encoding="UTF-8"?>
            <!DOCTYPE mapper
                    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
            <mapper namespace="com.itheima.dao.IUserDao">
                <!--配置查询所有,sql映射文件；配置了每一个sql语句，以及sql的封装规则等。-->
                <select id="findAll" resultType="com.itheima.domain.User">
                    select * from user
                </select>
            </mapper>
            ```
        * **在以上第三步中我们使用了mapping映射，也可以使用注解**
            * 此时，在resources目录下的IUserDao.xml可以删去。在dao接口的方法上使用@select()注解,也就是IUserDao这个接口变为：
            ```java
                @Select("select * from user")
                public interface IUserDao {
                    //查询所有操作
                    List<User> findAll();
                }
            ```
            * 在sqlmapconfig.xml这个文件中，如果使用注解来配置，则mapping处应该使用class属性指定被注解dao全限定类名：
            ```xml
           <mappers>
                <mapper class="com.itheima.dao.IUserDao"/>
            </mappers>
           ```
            * 在dao的目录下，建立一个impl的包，建立UserDaoImpl的类，其implement IUserDao这个接口，将findAll方法补充完整，调用```session.selectList()```这个方法,代码如下
            ```java
            public class UserDaoImpl implements IUserDao {
                private SqlSessionFactory factory;
                public UserDaoImpl(SqlSessionFactory factory){
                    this.factory=factory;
                }
                public List<User> findAll(){
                    //使用工厂创建sqlsession对象
                    SqlSession session=factory.openSession();
                    //使用session执行查询所有方法
                    List<User> users =session.selectList("com.itheima.dao.IUserDao.findAll");
                    //此处selectlist中的statement是在resources下的IUserDao.xml中namespace+id组成
                    session.close();
                    //返回查询结果
                    return users;
                }
            }
            ```
            * mybatistest.java文件此刻变为
            ```java
                public static void main(String[] args) throws Exception{
                    //读取配置文件
                    InputStream in= Resources.getResourceAsStream("SqlMapConfig.xml");
                    //创建sqlsessionfactory工厂
                    SqlSessionFactoryBuilder builder=new SqlSessionFactoryBuilder();
                    SqlSessionFactory factory=builder.build(in);
                    IUserDao userDao=new UserDaoImpl(factory);
                    //使用代理对象执行方法
                    List<User> users=userDao.findAll();
                    for (User user:users
                        ) {
                        System.out.println(user);
                    }
                    //释放资源
                    in.close();
                }
            ```
           
        ***
        * 环境搭建的注意事项：
            * 创建iuserdao.xml和iuserdao.java时，名称是为了和之前的知识保持一致，在mybatis中，它把持久层的操作接口名称和映射文件也叫做mapper，所以iuserdao和iusermapper是一样的。
            * 在idea中创建目录的时候，它和包是不一样的，包在创建时，com.itheima.dao时三级结构，目录在创建时，com.itheima.dao是一级结构
            * `mybatis的映射配置文件位置必须和dao接口的包结构相同`
            * `映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名`
            * `映射配置文件的操作配置（select），id属性的取值必须是dao接口的方法名`
            * 当我们遵从了以上三点后，我们在开发中就无需再写dao的实现类
            ---
    * mybatis的入门案例
        1. 读取配置文件，根据全局配置文件xml得到SqlSessionFactory
        2. 创建sqlssessionfactory工厂，使用SqlSession工厂，获取到SqlSession对象使用它来执行增删改查
        3. 创建sqlsession，一个SqlSession就是代表和数据库的一次会话，用完需要close
        4. 创建dao接口的代理对象
        5. 执行dao中的方法，使用sql 的唯一标识来告诉mybatis执行那个sql，sql都是保存在sql映射文件中。
        6. 释放资源
        * **注意事项**
            * 不要忘记在映射配置中告知mybatis要封装到哪一个实体类中
            * 配置的方式：指定实体类的全限定类名
        ```java
         //读取配置文件
        InputStream in= Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建sqlsessionfactory工厂
        SqlSessionFactoryBuilder builder=new SqlSessionFactoryBuilder();
        SqlSessionFactory factory=builder.build(in);
        //使用工厂生产sqlsession对象
        SqlSession session=factory.openSession();
        //使用sqlsession创建dao接口的代理对象
        IUserDao userDao=session.getMapper(IUserDao.class);
         //使用代理对象执行方法
        List<User> users=userDao.findAll();
         for (User user:users
            ) {
             System.out.println(user);
        }
         //释放资源
        session.close();
        in.close();
        ```
    * 自定义mybatis框架
* mybatis基本使用
    * 元素介绍：
        1. namespace：命名空间；指定为接口的全类名
        2. id：唯一标识
        3. resulttype：返回值类型
        4. #{id}：从传递过来的参数中取出id值
    ---
    * [全局配置文件：](https://mybatis.org/mybatis-3/zh/configuration.html)
        * 在mybatis-config.xml文件中 ``` <properties> </properties>```  properties来引入外部properties配置文件中的内容
            * resource：引入类路径下的资源
            * url：引入网络路径或者磁盘路径中的资源
        * ``` <settings> </settings>```包含很多重要的设置其中可以包含每一个```<setting>```
        
            * name：设置项名
            * value：设置项取值
            ```xml
            <settings>
                <setting name="mapUnderscoreToCamlCase" value="true"/>
                <!--驼峰命名法示例 -->
            </settings> 
            ```
* 自定义mybatis分析：
    * mybatis在使用代理dao的方式实现增删改查时做了两件事。1.创建代理对象。2.在代理对象中调用selectlist
    * 自定义mybatis能通过入门案例看到类
        
        * class resources  ;   class sqlsessionfactorybuilder   ;   interface sqlsessionfactory   ;   interface sqlsession
    * 关于sqlmapconfig.xml
        * 配置properties：
            * 可以在标签内部配置连接数据库的信息。也可以通过属性引用外部配置文件信息
            * resource属性：
                
                * 用于指定配置文件的位置，是按照类路径的写法来写，并且必须保存在类路径下
            * url属性：
                * 要求按照url的写法来写地址
                * url：uniform resource locator 统一资源定位符。它是可以唯一标识一个资源的位置。
                * 它的写法：
                ```
                http://localhost:8080/mybaitsserver/demo1servlet
                协议      主机    端口   url
                ```
                * uri:uniform resource identifier:统一资源标识符，它时在应用中可以唯一定位一个资源的。
        * typealias配置别名，它只能配置domain中的别名
            * type属性是实体类全限定类名，alias属性指定别名，指定之后不区分大小写
            ```xml
            <typealiases>
                <package name="com.itheima.daomain"></package>
            </typealiases>
            ```
        * mappers中的package
            
            * ```<package name="com.itheima.dao></package>```package标签是用于指定dao接口所在的包，当制定了之后就不需要再写mapper以及resource或者class了。
* 连接池及事务控制
    * 连接池：
        * 用于存储连接的一个容器。容器就是一个集合对象该集合必须是线程安全的，不能两个线程拿到统一连接。该集合还必须实现队列的特性。fifo
    * mybatis中的连接池：
        * 提供三种方式的配置
            * 配置位置：主配置文件中的sqlmapconfig.xml中的DataSource标签，type属性就是表示采用何种连接池方式
                * type取值：pooled采用传统的javax.sql.datasource中的连接池，mybatis中有针对规范的实现，unpooled虽然采用与pooled同样的接口，但是并没有使用池的对象，jndi采用服务器提供的jndi技术实现，来获取DataSource对象，不同的服务器所能拿到的DataSource是不同的，如果不是web或者maven的war工程，是不能使用的。tomcat连接池就是dbcp连接池。
    * mybatis中的事务
        * 什么是事务
        * 事务的四大特性ACID
        * 不考虑隔离性会产生的三个问题
        * 解决方法：四种隔离级别
          