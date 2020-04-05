## xml

1. 概念：extensible markup language 可标记语言
    * 可扩展：标签都是自定义的。<user> <student>
    * 功能：
        * 存储数据
            1. 配置文件
            2. 在网络传输
    * xml和html的区别
        1. xml的标签都是自定义的，html的标签是预定义。
        2. xml的语法严格，html语法宽松
        3. xml是存储数据的，html是展示数据
    * w3c：万维网联盟
2. 语法：
    * 基本语法：
        1. xml的文档名后缀：.xml
        2. xml的第一行必须定义为文档声明
        3. xml文档中有且仅有一个根标签
        4. 属性值必须使用引号（单双都可）引起来
        5. 标签必须正确关闭
        6. 标签名称区分大小写
    * 快速入门：

        ``` xml
            <?xml version='1.0'?>
            <users>  
            <!-- 此处users为根标签，id='1'位属性值 -->
            <user id='1'>
            <name>zhangsan</name>
            <age>23</age>
            </user>
            </users>
        ```

    * 组成部分：
        1. 文档声明
            1. 格式：< ? xml version='1.0' encoding='utf-8' standalone= 'no'>
            2. 属性列表：
                * version 版本，必须的属性
                * encoding 编码格式，告知解析引擎当前文档使用的编码方式。默认值：ISO-8859-1
                * standalone 是否独立 
                    * 取值
                        * yes：不依赖其他文件
                        * no：依赖其他文件
        2. 指令：
            * < ?xml-stylesheet type="text/css" href="a.css"?>
        3. 标签:标签名称自定义的
            * 规则：
                * 名称可以含字母、数字以及其他的字符
                * 名称不能以数字或者标点符号开始
                * 名称不能以字符 “xml”（或者 XML、Xml）开始
                * 名称不能包含空格
        4. 属性：
            属性id唯一
        5. 文本
    * 约束：规定xml文档的书写规则
        * 作为框架和使用者（程序员）：
            1. 能够在xml中引用约束文档
            2. 能够简单读懂约束文档
        * 分类：
            1. DTD：一种简单的约束技术
            2. schema：一种复杂的约束技术
3. 解析：操作xml文档，将文档中的数据读取到内存中
    * 操作xml文档
        1. 解析：将文档中的数据读取到内存中
        2. 写入：将内存中的数据保存到xml文档中，持久化的存储。
    * 解析xml的方式：
        1. DOM：将标记语言文档一次性加载进内存，在内存中形成一课树。
            * 优点：操作方便，可以操作crud
            * 缺点：比较消耗内存
        2. SAX：逐行读取，基于事件驱动
            * 优点：不占内存
            * 缺点：只能读取，无法crud
    * xml常见的解析器：
        1. jaxp：sun公司。支持dom和sax
        2. DOM4J
        3. Jsoup：jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。
        4. pull：Android系统内置解析器，sax方式的
    * Jsoup
        * 快速入门：
            1. 导入jar包
            2. 获取document对象
            3. 获取对应的标签element对象
            4. 获取数据
    * 例子：
        ```xml
        < ? xml version='1.0' encoding='UTF-8'>
        <!-- info:student.xml,In the same directory as src -->
        <students>
        <student number='heima_001'>
            <name>tom</name>
            <age>23</age>
            <sex>male</sex>
        </student>
        <student number='heima_002'>
            <name>jack</name>
            <age>18</age>
            <sex>female</sex>
        </student>
        </students>
        ```
        * 解析java代码：
        ```java
        String path=JsoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
        //解析xml文档，加载文档进内存，获取dom树-->Document
        Document document=Jsoup.parse(new File(path),"utf-8");
        //获取元素对象Element
        Elements elements=document.getElementsByTag("name");
        System.out.println(elements.size());
        //获取第一个那么的element对象
        Element element=elements.get(0);
        // get data
        String name=element.text();
        System.out.println(name);
        ```
    * 对象的使用：
        1. Jsoup：工具类，可以解析html或者xml文档，返回Document
            * parse(File in, String charsetName):解析xml或者html文件
            * parse(String html)解析xml或者html字符串
            * parse(String html, String baseUri)通过网络路径获取指定html或者xml对象
            ```java
            URL url=new URL("https://baike.baidu.com/item/jsoup");///网络中的资源路径
            Document document = Jsoup.parse(url,10000);
            ```
        2. Document：文档对象，代表内存中的dom树
            * 获取Element对象
                * getElementsByTag(String tagName)
                    ```java
                    Document document= Jsoup.parse(new File(path),"utf-8");
                    Elements element1=document.getElementsByTag("student");
                    ```
                * getElementsByAttribute(String key)
                * getElementsByAttributeValue(String key,String value)
        3. Elements：元素Element对象的集合，可以当做ArrayList< Element >来使用
        4. Element：元素对象
            1. 获取子元素对象
            2. 获取属性值
            3. 获取文本内容 
        5. Node：节点对象。
            * 是Document和Element的父类
    * 快捷查询方式：
        1. selector：选择器
            * 使用的方法
        2. xpath

