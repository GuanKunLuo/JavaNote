##### JAR

> 将相关的`.class` 文件打包一个`.jar` 文件后分享出去
>
> `JAR`：全程是`Java Archive`，使用`ZIP` 文件格式来打包
>
> 本质上是调用了JDK bin目录中的`jar.exe`
>
> 可以对`jar` 文件进行数字签名，防止别人篡改

##### Runnable JAR

> 可运行JAR：包含了程序入口`main` 方法的JAR

##### 双括号初始化

> 优点是代码简洁
>
> 缺点是多生成了一个匿名类
>
> 可能会影响`equals` 方法的正常使用
>
> 不建议使用

```java
//双括号初始化
//本质是生成了匿名类继承初始化对象，放在了对象的构造方法中
List<String> list = new ArrayList<String>() {{
    add("1");
    add("2");
}};

Person p = new Person() {{
    run();
}};

//class com.luo.Main$1
System.out.println(p.getClass());
//class com.luo.Person
System.out.println(p.getClass().getSuperclass());
```

##### TCP请求

> 在`Java` 中，使用`java.netSocket`、`java.net.ServerSocket` 可以实现TCP请求

```java
//服务器
public class TCPServer {
    public static void main(String[] args) {
        //端口号 16位
        try(ServerSocket server = new ServerSocket(8888)) {
            while (true) {
                Socket client = server.accept();
                //处理客户端
                new Thread(() -> {
                    try {
                        doClient(client);
                        client.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static void doClient(Socket client) throws IOException {
        try(ByteArrayOutputStream baos = new ByteArrayOutputStream();
            InputStream is = client.getInputStream()) {
            byte[] buffer = new byte[8192];
            int len;
            while ((len = is.read(buffer)) != -1) {
                baos.write(buffer, 0, len);
            }
            byte[] bytes = baos.toByteArray();

            String string = new String(bytes,"UTF-8");
            System.out.format("接收到客户端[%s]的数据：%s%n",client.getInetAddress(),string);
        }
    }
}

//客户端
public class TCPClient {
    public static void main(String[] args) throws IOException {

        Socket socket = new Socket("127.0.0.1", 8888);
        OutputStream os = socket.getOutputStream();

      	//写数据到服务器
        os.write("8888".getBytes("UTF-8"));
				//关闭输出（表明写给服务器的内容都写完了）
      	socket.shutdownOutput();
      
        os.close();
        socket.close();
    }
}
```

##### instanceof 和 isInstance()

> `instanceof`: 是比较运算符

`obj instanceof obj.class `

* 比较`obj` 是不是 `obj.class`这种类型，左边是传入对象，右边传入对象的Class

> `isInstance()`：是一个方法

`obj.getClass().isInstance(otherObj)`

* 判断`otherObj`对象是否能够转换为`obj.getClass()` 这个类型

##### properties

```java
//plugins.properties文件
//用来存储类对象键值对
#key=value
mouse=com.luo.Mouse
keyboard=com.luo.Keyboard

//鼠标
public class Mouse implements IUSB {
    @Override
    public void swapData() {
        System.out.println("鼠标在移动ing...");
    }
}

//键盘
public class Keyboard implements IUSB {
    @Override
    public void swapData() {
        System.out.println("键盘在打字ing...");
    }
}

//主板
public class MatherBoard {

    private static HashMap<String,IUSB> plugins = new HashMap<>();

  	//通过Properties文件和反射来加载和创建类对象，并完成安装的功能
    static {
      	//继承Hashtable
        Properties p = new Properties();
      
        //从classpath的根路径去加载plugins.properties文件，直接返回输入流
        try (InputStream inputStream = Thread.currentThread()
                .getContextClassLoader()
                .getResourceAsStream("plugins.properties");) {
          	
          	//加载，填充自己
            p.load(inputStream);

            //读取properties文件，创建USB组件对象
            for (Entry<Object, Object> entry : p.entrySet()) {
                String className = entry.getValue().toString();
              	//newInstance()方法调用的是对象的无参构造器方法
                Object c = Class.forName(className).newInstance();
                if (!(c instanceof IUSB)) continue;
                IUSB usbObject = (IUSB)c;
                plugins.put(entry.getKey().toString(),usbObject);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void unstall(String name) {
        plugins.remove(name);
    }

    public void doWork() {
        for (IUSB iusb : plugins.values()) {
            iusb.swapData();
        }
    }
}

//main()
//键盘在打字ing...
//鼠标在移动ing...
public static void main(String[] args) {
    MatherBoard matherBoard = new MatherBoard();
    matherBoard.doWork();
}
```

##### JavaBean

`JavaBean` 

> 是一种`Java`语言写成的可重用组件(类)

*必须遵循一定的规范*

1. 类必须使用`public`修饰
2. 必须保证有公共无参数构造器
3. 包含了属性的操作手段（给属性赋值，获取属性值）--- `get()`和`set()`

*分类*

1. 复杂：`UI`，比如`Button`，`Panel`，`Window` 类
2. 简单：`domain`，`dao`，`service` 组件
   * 封装数据，操作数据库，逻辑运算等

*成员组成*

1. 方法：`Method`

2. 事件：`event`：主要用于UI点击事件等

3. 属性：`property`

   * `property`： 表示状态，但是不是字段，是字段的操作方法(`getter`、`setter`)决定的

     框架中使用的大多是属性

##### 内省机制

```java
public class Person {
    private String name;
    private Integer age;
    private boolean man;

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public boolean isMan() {
        return man;
    }

    public void setMan(boolean man) {
        this.man = man;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", man=" + man +
                '}';
    }
}

public static void main(String[] args) throws Exception {
  //1.获取JavaBean的描述对象
  //
  BeanInfo beanInfo = Introspector.getBeanInfo(Person.class, Object.class);
  Person p = Person.class.newInstance();

  //2.获得JavaBean的属性描述类
  PropertyDescriptor[] beanDescriptor = beanInfo.getPropertyDescriptors();
  for (PropertyDescriptor descriptor : beanDescriptor) {
      //获取当前属性的名称
      System.out.println("属性名称："+ descriptor.getName());
      //获取当前属性的getter方法，返回Method对象
      System.out.println("getter：" + descriptor.getReadMethod());
      //获取当前属性的setter方法，返回Method对象
      System.out.println("write：" + descriptor.getWriteMethod());
      System.out.println("------------------------------------");

    	//如果当前的属性名称为name，那么获得方法后，进行set设置
      if ("name".equals(descriptor.getName())) {
          Method setter = descriptor.getWriteMethod();
          setter.invoke(p,"Mill");
      }
  }
}
```

**JavaBean和Map之间的相互装换**

> `JavaBean` 拥有多组`属性名`和`属性值`，每一个`属性名称`对应一个`属性值`，`属性名称`是不同的
>
> `Map`拥有多组`key-value`，每一个`key`对应一个`value`，`key`是不同的
>
> 如果把`JavaBean`中的属性名称看做是`Map`中的`key`，二者就是等价的

```java
public class BeanUtils {
    public static void main(String[] args) throws Exception {
        Person p = new Person(1111l,"luo",30);
        Map<String, Object> map = bean2Map(p);
        System.out.println("map: " + map);
      
      	Person person = map2Bean(map, Person.class);
        System.out.println("person: " + person);
    }

    //把JavaBean对象转换为Map
    public static Map<String,Object> bean2Map(Object beanObj) throws Exception {
        Map<String, Object> map = new HashMap<>();

        BeanInfo beanInfo = Introspector.getBeanInfo(beanObj.getClass(), Object.class);
        PropertyDescriptor[] pds = beanInfo.getPropertyDescriptors();

        for (PropertyDescriptor pd : pds) {
            Method method = pd.getReadMethod();
            map.put(pd.getName(), pd.getReadMethod().invoke(beanObj));
        }

        return map;
    }
  
  	//把map对象转换为JavaBean
    public static <T> T map2Bean(Map<String, Object> map, Class<T> beanType) throws Exception {
        T obj = beanType.newInstance();

        BeanInfo beanInfo = Introspector.getBeanInfo(obj.getClass(), Object.class);
        PropertyDescriptor[] pds = beanInfo.getPropertyDescriptors();
        for (PropertyDescriptor pd : pds) {
            //从Map中获取对应属性同名的key的值
            Object value = map.get(pd.getName());
            //调用setter方法设置属性值
            pd.getWriteMethod().invoke(obj, value);
        }

        return obj;
    }
}
```

##### Commons BeanUtils

> 是Apache软件基金会开源软件项目中的一个组件
>
> 用于操作JavaBean

```java
//mavean pom.xml
<dependency>
    <groupId>commons-beanutils</groupId>
    <artifactId>commons-beanutils</artifactId>
    <version>1.9.4</version>
</dependency>
  
//示例
public class BeanUtilsDemo {
    public static void main(String[] args) throws InvocationTargetException, IllegalAccessException {
        Person p = new Person();
        Map<String, Object> map = new HashMap<>();
        map.put("id", "");
        map.put("name", "luo");
        map.put("age", 30);
        map.put("bornDate", "1989-10-24");

        //beanUtils默认不支持String->Date转换，需要手动设置转换模式
        DateConverter dateConverter = new DateConverter();
        dateConverter.setPattern("yyyy-MM-dd");
        ConvertUtils.register(dateConverter, java.util.Date.class);

        //设置Long类型的默认值为null，否则默认值为0，与实际应用有冲突
        ConvertUtils.register(new LongConverter(null), Long.class);
				
      	//拷贝属性
        BeanUtils.copyProperties(p, map);
      	//Person{id=null, name='luo', age=30, bornDate=Tue Oct 24 00:00:00 CST 1989}
        System.out.println(p);
    }
}
```

##### 注解

> `Java 5`开始，`Java`开始对元数据的支持，也就是`Annotation`(注解/标签)。
>
> 所有的`Annotation`都是`java.lang.annotation.Annotation` 接口的子接口，所以`Annotation`是一种特殊的接口(枚举也是特殊的类）

```java
@interface Override {} ---> interface Override extends java.lang.annotation.Annotation{}
```

* 注解被用来为程序元素(类，方法，成员变量等)设置元数据。

*使用注解需要注意,必须有三方参与才有意义*

1. 得有注解标签

2. 被贴的程序元素
   * 类
   * 字段
   * 构造器
   * 方法

3. 由第三方的程序来赋予注解特殊的功能 （也是Java代码）

*JDK中内置的注解*

`@Override`：限定覆写父类方法

`@Deprecated`：标记已过时,不推荐使用，在`JDK 1.5`之前，使用文档注释来标记过时

`@SuppressWarings`：抑制编译器发出的警告

`@SuppressWarings(value="all")`、 `@SafeVarargs`：抑制堆污染警告(`Java 7`开始出现的)，该注解仅仅是抑制住编译器不要报警告，但是存在的风险依然存在，仅仅是不提示而已



**元注解**

`@Retention`

> 表示注解可以保存在哪一个时期
>
> 一般自定义的注解,使用`RUNTIME`(使用反射赋予注解功能)
>
> 保存的时期的值,封装在`RetentionPolicy` 枚举类中 

* *`SOURCE`*：注解只能存在于源文件中，编译之后，字节码文件中没有
* *`CLASS`*：  注解可以存在于源文件和字节码文件中，一旦加载进`JVM`，在`JVM`中不存在
* *`RUNTIME`*：注解可以存在于源文件，字节码文件，`JVM`中

---

`@Target`

> 表示注解可以贴在哪些位置(类,方法上,构造器上等等)
>
> 位置的常量封装在ElementType枚举类中

`ElementType.ANNOTATION_TYPE`：只能修饰`Annotation`

`ElementType.CONSTRUCTOR`：只能修饰构造方法

`ElementType.FIELD`：只能修饰字段（属性），包括枚举常量

`ElementType.LOCAL_VARIABLE`：只能修饰局部变量

`ElementType.METHOD`：只能修饰方法

`ElementType.PACKAGE`：只能修饰包（极少使用）

`ElementType.PARAMETER`：只能修饰参数

`ElementType.TYPE`：只能修饰类、接口、枚举

---

`@Documented`

> 使用`@Documented`标注的标签会保存到API文档中

---

`@Inherited`

> `@Inherited` 标注的标签可以被子类所继承



**自定义注解**

```java
//语法
@Target(ElementType.TYPE)			//标识该注解可以贴在类上/接口上
@Retention(RetentionPolicy.RUNTIME)		//标识该注解可以存在于JVM中(通过反射赋予功能)
public @interface 注解名称 {
        // 抽象方法-->属性
        返回类型 方法名称() default 值;
        ...
}

//示例
//注解定义
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface VIP {
    //注解中的属性类型只能是基本类型，String,Class,annotation,枚举，数组
    String name();
  	//使用default关键字来设置属性的默认值
    int age() default 18;
  	String[] hobby() default {"Java", "Swift"};
}

//使用注解
//使用注解时,当只有一个属性并且名称为value时，此时可以省略value=
@VIP(name="xx", age = 11, hobby={"Java", "Objective-C"})
public class Employee {

}
```

*获得注解*

```java
//如果存在该元素的指定类型的注释，则返回这些注释，否则返回 null。 
Annotation getAnnotation(Class annotationClass);
//返回此元素上存在的所有注释
Annotation[] getAnnotations(); 
//判断当前成员上是否标注了某一个注解
boolean isAnnotationPresent(Class annotationClass);

//示例
//第三方程序，来赋予注解功能
public class VIPDemo {
    public static void main(String[] args) {
        //需求：获取Employee上所有的注解
        //1.获取Employee字节码对象
        Class<Employee> cls = Employee.class;
        Annotation[] annotations = cls.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }
        
        //判断Employee类上是否使用VIP注解
        VIP vip = cls.getAnnotation(VIP.class);
        if (vip != null) {
            //name:luo
            //age:11
            //hobby[Java, Objective-C]
            System.out.println("name:" + vip.name());
            System.out.println("age:" + vip.age());
            System.out.println("hobby" + Arrays.toString(vip.hobby()));
        }
    }
}
```

*注解到底有何用*

>  取代繁琐/臃肿的配置文件

##### JUnit 测试

* `@Before`

* `@After`

* `@Test` ：测试单元

*执行顺序*

> `@Before` --> `@Test A` --> `@After` --> `@Before` --> `@Test B`--> `@After` --> ...

```java
//运行后打印结果
//init1
//test1
//end
//init1
//test2
//end
public class JunitTest {
    @Before
    public void init1() {
        System.out.println("init1");
    }

    @Test
    public void test2() {
        System.out.println("test2");
    }

    @Test
    public void test1() {
        System.out.println("test1");
    }
    
    @After
    public void end() {
        System.out.println("end");
    }
}
```

**使用断言进行测试**

> `idea` 自动生成测试类文件，在需要生成的类、接口名上，键入`option + enter`键，点击`create Test`

```java
//Math测试类
public class MathTest {

    private IMath math = new MathImpl();

    @Test
    public void plus() {
        int ret = math.plus(1, 2);
        //使用断言
        Assert.assertEquals(3, ret);
    }

    @Test
    public void divide2() {
        int ret = math.divide(12, 4);
        Assert.assertEquals(3, ret);
    }

    //断言该测试方法会报抛出ArithmeticException异常
    @Test(expected = ArithmeticException.class)
    public void divide() {
        int ret = math.divide(12, 0);
        Assert.assertEquals(0, ret);
    }

    //断言下面的测试方法在2秒内运行完成
    @Test(timeout = 2000)
    public void divide3() {
        
    }
}
```

##### XML

> XML(eXtensible Markup Language)，是一种可扩展的标记语言，类似`HTML`
>
> `XML`技术是`W3C`组织(World Wide Web Consortium万维网联盟)发布的，目前遵循的是`W3C`组织于2000年发布的XML1.0规范
>
> `XML` 的树状结构简单,清晰，无论是人还是计算机都能轻松解析
>
> `XML` 作为一种公订的、开放的标准，不受知识产权的限制

* `HTML`显示页面，网页

* `XML` 传输数据，而非显示数据
  * `XML` 标签没有被预定义，需要用户自行定义标签

*为什么要学`XML`*

* `XML`是一种通用的数据交换格式

* 许多项目都采用XML作为数据交换格式

* 掌握XML是软件开发人员的一项基本技能

* `Struts1.x`, `Struts2.x`, `Spring`, `Hibernate`, `Mybatis` 等任意一个`Java EE` 框架中都可用`XML`做配置文件

*XML语法*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--在编写XML文档时，需要先使用文档声明来声明XML文档。且必须出现在文档的第一行-->

<!-- 一个XML文档必须有且仅有一个根标签，不允许标签嵌套，区分大小写。-->
<contacts>
    <linkman id="1">
        <name>A</name>
        <email>A@sina.com</email>
        <address>成都</address>
        <group>As</group>
    </linkman>
    <linkman id="2">
        <name>B</name>
        <email>B@sina.com</email>
        <address>北京</address>
        <group>B</group>
    </linkman>
</contacts>
```

*XML约束*

* `dtd`
* `schema`

```dtd
//dtd文件
<!ELEMENT contacts (linkman+)>
<!ELEMENT linkman (name, email, address, group)>
<!ELEMENT name (#PCDATA)>
<!ELEMENT email (#PCDATA)>
<!ELEMENT address (#PCDATA)>
<!ELEMENT group (#PCDATA)>

//xml文件添加
<!DOCTYPE contacts SYSTEM "constactsDtd.dtd">
```

*XML解析*

**`DOM`：Document Object Model（`文档对象模型`）**

* 使用面向对象的方式，把`XML` 文件中的结构使用对象来表示

> 在`XML`中，一切皆节点（`Node`）

* `Document`：文档节点（`XML` 文件）
* `Element`：元素节点（使用`<>`表示标签）
* `Attr`：属性节点（元素上属性名="属性值"）
* `Text`：文本节点（元素之间的内容）

*特点*

* 在加载的时候，一次性的把整个`XML文档`加载进内存，在内存中形成一颗树（`Document`对象）

* 之后使用代码操作`Document`，其实操作的是内存中的`DOM树`，和本地磁盘中的`XML文件`没有直接关系
  * 比如：保存一个联系人，仅仅是内存中多了一个联系人，但是在`XML文件`中没有新增的痕迹

* 除非做同步操作（把内存中的数据更新到XML文件）
  * 增、删、改、操作完之后，都需要做同步操作

*缺点*

* 若`XML`文件过大，可能造成内存溢出

*注意*

* 使用`import`导入`类`/`接口`，注意使用`org.w3c`组织的

```java
private File f = new File("/Users/luoguankun/Documents/maven_java_learn_project/src/main/java/com/luo/xml/dtd/contacts.xml");

@Test
public void testGetDocument() throws Exception {
    DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
    DocumentBuilder documentBuilder = dbf.newDocumentBuilder();
    //在内存中创建一个新的Document对象
    //Document document = documentBuilder.newDocument();
    //从文件读取一个Document对象到内存中
    Document document = documentBuilder.parse(f);
}
```

---

*操作DOM*

```java
//获取操作
//1.获得XML文件的Document文档对象
Document document = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(file);
//2.获得根Element元素对象
Element root = document.getDocumentElement();

//3.获得第二个linkman节点
//调用者不同，范围不同
//获取文档中所有linkman元素，从整个文档中去寻找
Element elForDoc = (Element) document.getElementsByTagName("linkman").item(1);
//获取当前根元素中所有的linkman元素，从当前元素的子元素中去寻找.
Element elForElement = (Element) root.getElementsByTagName("linkman").item(1);

//4.获取linkman元素下面的name子元素
Element nameElement = (Element) elForElement.getElementsByTagName("name").item(0);

//5.获取name元素的文本内容
Assert.assertEquals("B", nameElement.getTextContent());

//======================================================================

//修改操作
Document document = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(file);
Element root = document.getDocumentElement();
Element element = (Element)root.getElementsByTagName("linkman").item(0);
Element emailElement = (Element) element.getElementsByTagName("email").item(0);
//设置内存中的Element
emailElement.setTextContent("389539056@qq.com");

//同步操作，把内存中的数据同步更新到磁盘的XML中
TransformerFactory factory = TransformerFactory.newInstance();
Transformer transformer = factory.newTransformer();
//来源：内存中的Document对象
Source source = new DOMSource(document);
//目标：磁盘中的contacts.xml文件
Result outPutTarget = new StreamResult(file);
transformer.transform(source, outPutTarget);

//======================================================================

//新增DOM操作
Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(file);
Element root = doc.getDocumentElement();
Element newEement = doc.createElement("linkman");

HashMap<String,String> nodeMap = new HashMap<>();
nodeMap.put("name","1");
nodeMap.put("email","1");
nodeMap.put("address","1");
nodeMap.put("group","1");

for (Map.Entry<String, String> entry : nodeMap.entrySet()) {
    Element element = doc.createElement(entry.getKey());
    element.setTextContent(entry.getValue());
    newEement.appendChild(element);
}

root.appendChild(newEement);

//同步操作，把内存中的数据同步更新到磁盘的XML中
Transformer transformer = TransformerFactory.newInstance().newTransformer();
Source source = new DOMSource(doc);
Result target = new StreamResult(file);
transformer.transform(source, target);


//======================================================================

//属性操作

//删除操作
```

---

*DOM遍历*

```java
@Test
public void testIteratorXML() throws Exception {
    Document doc = DocumentBuilderFactory
            .newInstance()
            .newDocumentBuilder()
            .parse(file);
    Element root = doc.getDocumentElement();

    parse(root);
}

public void parse(Element element) {
    System.out.print("<" + element.getNodeName() + ">");
    if (element.hasChildNodes()) {
        NodeList nodeList = element.getChildNodes();
        int length = nodeList.getLength();
        for (int i = 0; i < length; i++) {
            Node node = nodeList.item(i);
            short nodeType = node.getNodeType();
            if (nodeType == node.ELEMENT_NODE) {
                //元素
              	//递归
                parse((Element)node);
            }

            if (nodeType == node.TEXT_NODE) {
                //文本
                System.out.print(node.getNodeValue());
            }

            if (nodeType == node.COMMENT_NODE) {
                //注释
                System.out.print("<!--" + node.getNodeValue() + "-->");
            }
        }
    }
    System.out.println("</" + element.getNodeName() + ">");
}
```

*节点类型及描述*

https://www.w3school.com.cn/xmldom/dom_nodetype.asp

##### JDBC

> `Java DataBase Connectivity`
>
> `JDBC` 是一种用于执行`SQL` 语句的`Java API`，可以为多种`关系数据库`提供统一访问
>
> 它由一组用`Java`语言编写的`类` 和`接口` 组成
>
> `JDBC` 提供了一种`基准`，据此可以构建更高级的工具和接口，使数据库开发人员能够编写数据库应用程序.
>
> 在`Java`中，数据库存取技术只能通过`JDBC`访问数据库，`JDBC`是`Java`访问数据库的基石,其他技术都是对`JDBC`的封装.
>
> `JDBC`本身是`Java`连接数据库的一个标准,是进行数据库连接的抽象层,由`Java`编写的一组类和接口组成，接口的实现由各个数据库厂商来完成

`JDBC`访问数据库的形式主要有两种：

1. 直接使用`JDBC`的`API`去访问数据库服务器(`MySQL/Oracle`)
2. 间接地使用`JDBC`的`API`去访问数据库服务器(底层依然是`JDBC`)

*连接数据库*

```java
//pom.xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.20</version>
    <scope>runtime</scope>
</dependency>

import java.sql.Connection;
import java.sql.DriverManager;

//加载注册驱动
//会把com.mysql.cj.jdbc.Driver这份字节码加载进JVM
//当一份字节码被加载进JVM时,就会执行该字节码中的静态代码块
Class.forName("com.mysql.cj.jdbc.Driver");

/*	
 * 获取连接
 * url : 连接数据库的URL  
 *       jdbc:mysql://localhost:3306/jdbcdemo
 *       如果连接的是本机的MySQL,并且端口是默认的3306,则可以简写为jdbc:mysql:///jdbcdemo
 * username:    数据库用户名 
 * password:    数据库密码
 */
Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/jdbcdemo"
                                                    ,"root"
                                                    ,"meimima123");
```

*操作*数据库

> 口诀: `贾琏欲执事`

1. 贾：加载注册驱动

2. 琏：获取连接对象

3. 欲：创建语句对象
4. 执：执行SQL语句
5. 事：释放资源

```java
//DDL操作
Class.forName("com.mysql.cj.jdbc.Driver");

try (Connection connection = DriverManager.getConnection("jdbc:mysql:///jdbcdemo","root","meimima123");
     Statement statement = connection.createStatement();) {

    //执行一个静态的SQL语句
    String sql = "CREATE TABLE t_student (id bigint PRIMARY KEY AUTO_INCREMENT,name varchar(20),age int(11));";

    System.out.println(statement.executeUpdate(sql));
}

-----------------------------------------------------------------------------------------------

//DML操作
Class.forName("com.mysql.cj.jdbc.Driver");

try (Connection connection = DriverManager.getConnection("jdbc:mysql:///jdbcdemo","root","meimima123");
     Statement statement = connection.createStatement();) {
  
  	//插入
    String sql1 = "INSERT INTO t_student (name,age) VALUES ('乔峰',30)";

  	//更新
  	String sql2 = "UPDATE t_student SET name = '西门吹雪',age = 28 WHERE id = 1";
  
    //删除
  	String sql3 = "DELETE FROM t_student WHERE id = 1";
  
  	int rowsCount =  statement.executeUpdate(sql1);
}

-----------------------------------------------------------------------------------------------
  
//DQL操作
Class.forName("com.mysql.cj.jdbc.Driver");

Connection connection =DriverManager.getConnection("jdbc:mysql:///jdbcdemo", "root", "meimima123");
Statement statement = connection.createStatement();

String sql = "SELECT id,name,age FROM t_student";

//遍历
ResultSet resultSet = statement.executeQuery(sql);
while (resultSet.next()) {
    int id = resultSet.getInt("id");
    String name = resultSet.getString("name");
    int age = resultSet.getInt("age");
    System.out.println("id:"+id+"，"+"name:"+name+"，"+"age:"+age);
}

resultSet.close();
statement.close();
connection.close();
```

##### DAO

> `DAO(Data Access Object)`是一个数据访问接口，与数据库打交道
>
> 夹在`业务逻辑`与`数据库资源`中间
>
> 封装了数据的增删改查(`CRUD`)操作

#####  添加个标题

