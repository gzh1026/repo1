# 今日内容：

1. servlet
2. http协议
3. request

## servlet：

1. 概念
2. 步骤
3. 执行原理
4. 生命周期
5. servlet3.0
6. servlet的体系结构    
    * genericservlet--抽象类
        * servlet--接口：将servlet接口中其他方法做了默认空实现，只将service（）方法作为抽象。将来定义servlet类时，可以继承genericservlet，实现service（）方法即可。其他方法想复写再复写。(详情可以看genericservlet的源码。)
            ```java
            @WebServlet("/demo2")
            public class ServletDemo2 extends GenericServlet {
            @Override
            public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
                System.out.println("demo2");
                }
            }
            ```
    * httpservlet--抽象类
        * 对http协议的一种封装，简化操作
            1. 定义类继承httpservlet
            2. 复写doGet/doPost方法
7. servlet相关配置
    1. urlpartten：servlet访问路径  
        1. 一个servlet可以定义多条访问路径：` @WebServlet({"/d4","/dd4","/ddd4"}) `
        2. 路径定义规则：` /xxx(/demo4), /xxx/xxx(/user/demo4),  *.do(demo4.do)不可以加/ `

## HTTP
* 概念：Hyper Text Transfer Protocol 超文本传输协议
    * 传输协议：定义了客户端和服务器端通信时发送数据的格式
    * 特点：
        1. 基于TCP/IP的高级编程
        2. 默认端口号是80
        3. 基于请求/相应模型：一次请求一次相应
        4. 无状态的：每次请求之间相互独立，不能交互数据