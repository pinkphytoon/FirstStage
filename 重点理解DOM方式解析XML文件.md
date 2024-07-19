# 复习

# 1 掌握jar文件导入

### 	在项目向创建lib文件夹 -->>> 将指定的jar文件复制粘贴到lib文件夹下 -->>> 在lib文件夹上右键单击 -->>> Add as Library......

# 2 会使用JUnit单元测试框架

## 2-1 会自动生成JUnit单元测试类

### 	在项目向创建test文件夹 -->>> 在test文件夹上右键单击 -->>> Mark Directory as -->>> Test Source Root

## 2-2 会自定义测试方法

```java
@Test
public void 方法名() [throws 异常[,异常,.....]]{
    参与单元测试的代码;
}
```

# 3 掌握对.properties文件的解析

## 3-1 在项目下创建resource文件夹 -->>> 在resource文件夹上右键单击 -->>> Mark Directory as -->>> Resources Root

## 3-2 注释：#

## 3-3 存储方式和特点：是以KEY-VALUE键值对形式存储，只支持String类型；对应Java中封装好的Properties集合类（是Map集合的一个具体实现类）

## 3-4 现代工艺解析.properties文件

```java
// 通过指定类的字节码对象调用方法获取类加载器
ClassLoader cl = 类名.class.getClassLoader();

// 通过ClassLoader类加载器调用方法，将指定配置文件中的数据加载到字节输入流对象中
InputStream is = cl.getResourceAsStream(String fileName);

// 实例化Properties集合对象
Properties p = new Properties();

// 通过Properties集合对象调用方法，将指定流对象中的数据加载到集合中
p.load(is);

// 通过Properties集合对象调用方法，根据指定的KEY获取VALUE
String value = p.getProperty(String key);

// 关闭流资源对象
if(is != null){
    is.close();
}
```

# 4 掌握定义.xml文件的规范

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 注释 -->
<根标签名>
    <一级子标签>
        <二级子标签>文本内容</二级子标签>
        <单标签/>
    </一级子标签>
</根标签名>
```

# =============

# 重点理解DOM方式解析XML文件

## DOM：Document Object Model，文档对象模型

## 作用：将指定的配置文件，转换成Java中的文档类（Document）,通过文档类的对象，按照指定的规则调用方法完成对配置文件的操作



# 使用DOM方式解析配置文件的前提

## 1、文件中存储数据的方式必须使用的是标签🏷（.xml和html）

## 2、操作文档语言必须支持面向对象思想（Java和JavaScript）



# 两种配置文件：

## 1、.XML

## 2、..properties



# 原理树——DOM树

![image-20240718090339248](E:\JAVA58\二阶段\0718\img\image-20240718090339248.png)

# 操作步骤

## 1、将指定的xml文件 => 成Java类

## 2、通过指定类的 对象.方法 ，操作配置文件中的数据

#### （对象中操作好数据，再放到文件中）

## 3、将java代码中操作的结果，反馈给.xml文件



# 理解综合案例:使用DOM方式对指定的XML文件进行CRUD操作

# 1、需求：创建student.xml文件，储存三个学生信息，要求包含学号、姓名、专业、邮箱

# 2、student.xml文件定义

![image-20240718202549279](E:\JAVA58\二阶段\0718\img\image-20240718202549279.png)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Student>
    <!--XML文件中严格区分大小写！！！-->
    <student>
        <id>001</id>
        <name>张玲慧</name>
        <subject>张老汉</subject>
        <email>zhanglinghui@wolfcode.cn</email>
    </student>
    <student>
        <id>002</id>
        <name>张金</name>
        <subject>佛系小子</subject>
        <email>zhangjin@wolfcode.cn</email>
    </student>
    <student>
        <id>003</id>
        <name>胡仁坤</name>
        <subject>又是破碎的一天</subject>
        <email>hurenkun@wolfcode.cn</email>
    </student>
</Student>
```

# 3、按照指定的需求完成对xml文件的操作

![image-20240718220631244](E:\JAVA58\二阶段\0718\img\image-20240718220631244.png)

```java
public class XMLByDOMTest {
    //在控制台上打印打印第二个学生的姓名
    @Test
    public void getSecondStuName() throws Exception {
        /*将指定的XML文件转换成Document类对象——共3步骤*/
        /*第一步*/
        //获取文档构建者工厂类对象——获得DBF工厂的零件名叫dbf
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        //通过文档构建者工厂类对象调用方法——通过零件名获得了如何建筑的方法
        DocumentBuilder db = dbf.newDocumentBuilder();
        //通过文档构建者对象调用方法，将指定的XML文件转换成Document类对象——再把方法parse返回在Dom文档（有路径）里面
        Document doc = db.parse(new File("resource/student.xml"));
        /*第二步*/
        //通过文档对象调用方法获取根标签——文档中得到文档元素名叫root
        Element root = doc.getDocumentElement();
        //通过根标签获取下面的所有指定名字的一级子标签，再通过方法获取指定位置上的子标签
        Element secondStu = (Element) root.getElementsByTagName("student").item(1);
        //再学生标签对象获取下面指定的名字的标签，再通过方法获取指定位置上的子标签
        Element name = (Element) secondStu.getElementsByTagName("name").item(0);
        //通过指定元素对象调用方法获取文本内容
        System.out.println("name = " + name.getTextContent());
    }
}
```

![image-20240718212251364](E:\JAVA58\二阶段\0718\img\image-20240718212251364.png)

```java
@Test
//修改第三个学生为熬大夜
public void editThirdStuSubject() throws Exception {
    DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
    DocumentBuilder db = dbf.newDocumentBuilder();
    Document doc = db.parse(new File("resource/student.xml"));
    Element root = doc.getDocumentElement();
    //得到一个包含所有名为"student"的元素的列表。然后，我们使用索引2来获取第三个学生元素
    Element secondStu = (Element) root.getElementsByTagName("student").item(2);
    //只有一个名为"subject"的子元素，所以我们可以使用索引0来获取它
    Element subject = (Element) secondStu.getElementsByTagName("subject").item(0);
    //将名为"subject"的子元素的文本内容设置为"熬大夜"
    subject.setTextContent("熬大夜");
    System.out.println("subject = "+subject.getTextContent());
    /*
    * 上述操作，只是代码在java后台实现修改操作
    * 并没有将修改的数据回写给XML文件，因此XML文件中的诗句没有被
    * 改动，需要回写XML👇(增删改)
    * */
    //创建回写工厂类对象
    TransformerFactory tf = TransformerFactory.newInstance();
    //通过回写工厂类对象调用方法，获取回写类对象
    Transformer t = tf.newTransformer();
    //通过回写类对象调用方法，将后台的数据写入到指定的文件中
    t.transform(new DOMSource(doc),new StreamResult(new File("resource/student.xml")));
}
```

![image-20240718220656480](E:\JAVA58\二阶段\0718\img\image-20240718220656480.png)

# 4、工具类的封装思想

## 为啥使用工具类（方便）？

## 当操作代码的步骤重复使用，且步骤较多，建议将多步骤操作单独*<u>封装到指定的方法</u>*中，而方法只能定义在类中，因此称这个类为工具类

#### *就好比说我要一部手机*，需要各种备件，买配件的话是比较麻烦的，*既然如此就创造一个工厂*，让工厂里面做手机；So *当你获取这个对象也好*，获取技术也好，都是比较麻烦的，*这样才会使用工具类封装说的思想*



# 既然都是重复使用，为啥不能用循环呢？

### 循环时在规定的范围内连续多次的重复创建

### 而这个工具类是在不同的方法里去创建一次





# 工具类设计规则

### 1、类可以是抽象类或接口

##### （接口：在stream的时候讲     ；   抽象类：不是抽象类的话用户可以通过JVM虚拟机提供的默认无参构造器来实例化对象，设计为抽象类那么它的抽象类就是无效的，能更方便）

### 2、类中所有成员一定被static修饰



# 工具类的封装

## 1、将指定的配置文件的路径定义在 .properties文件中；文件名是固定的（源码权限问题）—解决硬编码— .properties文件

![image-20240718223228104](E:\JAVA58\二阶段\0718\img\image-20240718223228104.png)

![image-20240718223426428](E:\JAVA58\二阶段\0718\img\image-20240718223426428.png)

### .properties配置文件在操作.xml文件之前就应该加载好（只加载一次）所以写在static代码块中



## 2、封装工具类——死都要会！！！

```java
public abstract class DomUtils {
    private static String filePath;

    static {
        //先获取当前类的类加载器，用这个类的class的得到class加载的方法
        ClassLoader cl = DomUtils.class.getClassLoader();
        //尝试从类路径下的资源文件夹resource中加载名为fileUrl.properties的文件。
        cl.getResourceAsStream("resource/fileUrl.properties");
        //创建了一个Properties对象，如果要从fileUrl.properties文件中加载属性，需要使用p.load(InputStream)方法。
        Properties p = new Properties();
        /*当类被加载时，会尝试从资源文件夹中加载fileUrl.properties
         * 文件，并将其中的filePath属性值赋给filePath变量。
         * */
    }
}
```

![image-20240719190810389](E:\JAVA58\二阶段\0719\img\image-20240719190810389.png)

```java
public abstract class DomUtils {
    private static String filePath;
    //properties配置文件的值随便改，里面是根据名字来获取的
    private static Document doc;
    //👆 后加入的，因为在获取文档对象和重写中都需要这个，把它设置为类成员
    
    static {
        ClassLoader cl = DomUtils.class.getClassLoader();
        InputStream is = cl.getResourceAsStream("fileUrl.properties");
        Properties p = new Properties();
        try {
            //加载配置文件
            p.load(is);
            //获取配置文件中的文件路径—属性值
            filePath = p.getProperty("filePath");
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (is != null) {
                    //关闭流资源
                    is.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    //获取文档对象
    public static Document getDocument() {
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        DocumentBuilder db = null;
        Document doc = null;
        try {
            //将指定的XML文件转换成Document类对象——3步
            db = dbf.newDocumentBuilder();
            doc = db.parse(filePath);//FilePath上面已经获取到了，接下来改配置文件里面的值对它不影响
            //new File("resource/student.xml)"不用了
        } catch (Exception e) {
            e.printStackTrace();
        }
        return doc;
    }

    //回写——写到XML文件里面
    public static void writertoxml() {
        TransformerFactory tf = TransformerFactory.newInstance();
        Transformer t = null;
        try {
            t = tf.newTransformer();
            t.transform(new DOMSource(doc), new StreamResult(new File(filePath)));
        } catch (Exception e) {//抛异常就整Exception
            e.printStackTrace();
        }
    }
}
```

# 5、使用工具类完成对student.xml文件的增和删操作

### ①增加

![image-20240719212038055](E:\JAVA58\二阶段\0718\img\image-20240719212038055.png)

```java
public class XMLByDOMUtilsTest {
    //名为DomUtils的类中调用了一个名为getDocument()的方法，
    //并将返回的结果赋值给一个名为doc的Document类型的变量。
    private Document doc = DomUtils.getDocument();

    //向student.xml文件中添加一个学生信息
    @Test
    public void addStudent() {
        //通过文档对象分别创建student、id、name、subject、email标签
        Element student = doc.createElement("stduent");
        Element id = doc.createElement("id");
        Element name = doc.createElement("name");
        Element subject = doc.createElement("subject");
        Element email = doc.createElement("email");
        //分别给指定student、id、name、subject、email设置文本内容
        id.setTextContent("004");
        name.setTextContent("栾俊浩");
        subject.setTextContent("王八蛋");
        email.setTextContent("luanjunhao@wolfcode.cn");
        //把设置好的文本内容去追加到student的下面
        student.appendChild(id);//加个孩子
        student.appendChild(name);
        student.appendChild(subject);
        student.appendChild(email);
        //再把student的整体追加到根标签中，获取根标签
        Element root = doc.getDocumentElement();
        //将student标签追加到根标签下
        root.appendChild(student);
        //通过DOMUtils工具类调用方法，回写XML文件
        DomUtils.writertoxml();
    }
}
```

![image-20240719204142475](E:\JAVA58\二阶段\0718\img\image-20240719204142475.png)

### ②删除

```java
//删除第一个学生
@Test
public void deleteFirstStudent() {
    //获取根标签
    Element root = doc.getDocumentElement();
    //通过调用根标签调用方法根据指定标签名获取所有标签，再通过下标获取指定的元素对象
    Element firstStu = (Element) root.getElementsByTagName("student").item(0);
    //通过父一级标签调用方法删除指定的子一级标签
    root.removeChild(firstStu);
    //通过DOMUtils工具类调用方法，回写XML文件
    DomUtils.writertoxml();
}
```



# 理解反射机制

## 官方定义：通过指定类的<u>*字节码文件*</u>对象，动态操作类成员



## 好处：一定程度上去降低堆栈内存的损耗



## 运行原理：

![image-20240718105510149](E:\JAVA58\二阶段\0718\img\image-20240718105510149.png)

### 来一个class字节码文件,classload统一管理不过来，所有调用者可以通过Class类中提供的方法操作指定类成员



## Class类的获取方式!!!!!!!!!!（3点，重点掌握前两种）

```java
//通过指定类名.class获取Class类对象
Class cl = 类名.class;

//通过Class类名调用方法，根据指定类的全限定路径名获取Class类对象，全限定路径名：包名.类名
Class cL = Class.forName(String classPath);

//通过Object类提供的方法获取class类的对象
public final Class getclass();
```



# 反射机制的前提：类必须符合JavaBean规范



# 使用反射机制操作类成员

![image-20240719221040454](E:\JAVA58\二阶段\0718\img\image-20240719221040454.png)

## 按照JavaBean规范自定义类

![image-20240719221546387](E:\JAVA58\二阶段\0718\img\image-20240719221546387.png) 

```java
package cn.wolfcode.domain;

import lombok.*;

//使用Lombok插件提供的注解自动生成有参、无参构造器、get/set方法、toString方法
//👇会爆红，之后alt+回车
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@ToString

//@Data => 出构造器以外的所有类中需要的方法：get/set方法、equals方法、hashcode方法
public class Student {
    private Long id;
    private String name;
    private String subject;
    private String email;
    
}
```

## 使用反射机制操作类成员 — 其中获取无参构造器（死都要会）

#### 构造器的作用：实例化对象、有参构造器完成值的传递、无参构造器只做实例化的操作

![image-20240720001348563](E:\JAVA58\二阶段\0718\img\image-20240720001348563.png)

```java
public class Student_ReflectTest {

    //获取无参构造器
    @Test
    public void getNoArgsConstructor() throws Exception {
        //                                       👆 非法权限异常  IllegalAccessException
        //获取Class类对象
        Class<Student> cl = Student.class;
        //通过Class类对象调用方法，获取无参构造器
        Student student = cl.newInstance();
        student.setId(1L);
        student.setName("张玲慧");
        student.setSubject("软件工程");
        student.setEmail(student.toString());
    }
}
```

```java
public class Student_ReflectTest {
    //获取有参构造器
    @Test
    public void getAllArgsConstructor() throws Exception {
        Class<Student> cl = (Class<Student>) Class.forName("cn.wolfcode.domain.Student");
        //            👆   手搓
        Constructor<Student> c = cl.getConstructor(Long.class, String.class, String.class, String.class);
        Student zlh = c.newInstance(2L, "张玲慧", "Java", "zhanglinghui@wolfcode");
        System.out.println(zlh.toString());
        /*
        * 这段代码是一个JUnit测试方法，用于测试Student类的构造函
        * 数。它首先通过反射获取Student类的Class对象，然后获取一
        * 个具有四个参数（Long, String, String, String）的构
        * 造函数。接着，使用这个构造函数创建一个新的Student对象，并
        * 打印其信息。
        * */
    }

    //操作有参方法
    @Test
    public void testYesParameterMethod() throws Exception {
        //获取Student类的Class对象
        Class<? extends Student> cl = new Student().getClass();
        //通过反射获取Student类中名为setName的方法，该方法接受一个String类型的参数。
        Method setName = cl.getDeclaredMethod("setName", String.class);
        /*
         * 通过Method类对象调用方法,为获取的有参方法传递参数
         * public Object invoke(Object obj,Object... values)
         * 参数详解：
         *   obj — 表示当前的方法所在类的对象
         *   values — 表示传递的参数值
         * */
        //使用反射创建一个新的Student对象。
        Student student = cl.newInstance();
        //调用setName方法，将新创建的Student对象的name属性设置为"zlh"。
        setName.invoke(student, "zlh");
        //输出新创建的Student对象的name属性值。
        System.out.println(student.getName());
    }

    //操作成员字段
    @Test
    public void testFiled() throws Exception {
        //它获取了Student类的Class对象
        Class<Student> cl = Student.class;
        //通过反射获取了subject字段
        Field subject = cl.getDeclaredField("subject");
        /*
         * 当操作的是私有化的类成员时，需要先获取操作权限，在操作
         * public void setAccessible(boolean flag)
         * 默认值为false  即没有操作权限
         * */
        //它将subject字段设置为可访问
        subject.setAccessible(true);
        //它创建了一个Student类的实例
        Student student = cl.newInstance();
        /*
        * 当前这样编写代码，会抛出非法权限异常，原因是类的成员字段
        * 全部私有化，因此推断出Field类中提供的set方法底层是通过
        * 对象名.属性名 = 值的方法进行赋值操作
        * */
        //并将subject字段的值设置为"java宗师"。
        subject.set(student,"java宗师");
        //最后，它打印出student对象的subject字段值。
        System.out.println(student.getSubject());
    }
}
```



# 面试题：简述什么是反射机制？使用反射机制的好处和前题是什么？

## 含义：通过指定类的字节码文件对象，动态操作类成员

## 好处：一定程度上降低堆栈内存的损耗

## 前提：类必须符合JavaBean定义规范

# ---------------

# 反射机制的概述

# 创建Class类对象的方式

# 使用反射机制操作无参构造器和有参方法

# 内省机制

# 重点掌握内省机制和反射机制的区别与联系

# 理解使用内省机制操作成员字段

# 理解JavaBean和Map集合之间的转换