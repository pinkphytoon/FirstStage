# 复习：

# 1 Lambda表达式

## 1-1 重点掌握特点（改造原则）

## 1-2 会使用Java定义好的常用4个函数式的接口

### 	①Consumer<T> ：消费型接口，void accept(T t)，只进不出 —— 

### 	②Supplier<T>：供给型接口，T get()，只出不进——获得

### 	③Function<T,R>：函数型接口，R apply(T t)，有进有出，重点关注返回的结果——转换

### 	④Predicate<T>：断言型接口，boolean test(T t)，有进有出，重点关注条件过滤

## 1-3 拓展（了解）

### 	①方法引用

### 		Ⅰ :: 引用运算符

### 		Ⅱ 前提：必须支持Lambda表达式改造

### 		Ⅲ 引用方法的特征：无参方法、不能进行连续引用

### 		Ⅳ 引用格式

```java
对象 :: 方法名
类名 :: 静态方法
类名 :: 方法名
类名 :: new
```

### 	②延迟加载

### 	③更强大的接口

### 		Ⅰ 提出default和static修饰的带有方法体的方法

### 		Ⅱ 当类实现多个接口，且多个接口中带有同名的default方法时，也需要重写，引用的顺序，与实现接口的顺序一致

### 		Ⅲ 适用场景：一般用于自定义工具类，且工具类中不需要定义成员变量

# 2 Stream对象API

## 2-1 理解什么是Stream对象

## 2-2 重点掌握Stream对象创建方式

```java
// 将所有的单列集合转换成Stream对象 -- 单列集合类提供
public stream stream(); -- 会按照集合中存储的顺序显示数据
public stream parallelStream(); -- 会随机显示集合中的元素，适用于数据量较大的操作
    
// 将指定的数组转换成Stream对象 -- Arrays类提供
public static stream stream(数组);

// 将任意类型的多个散数据转换成Stream对象 -- Stream类提供
public static Stream of(T ... t);

// 了解知道 -- 创建无限流 -- Stream类提供
public static Stream iterate(T seed,UnaryOperator uo);
public static Stream generate(Supplier<T> supplier);
```



## 2-3 整理ClassLoader类、Class类、Stream类对象创建方式

## 2-4 重点掌握两组API

```java
// 筛选和切片
集合对象.stream() // 将指定的集合对象转换成Stream对象
    .filter(Predicate<T> predicate) // 根据用户条件进行过滤操作（可以过滤多次）
    .distinct() // 去掉Stream对象中的重复元素
    .limit(long maxSize) // 保留Stream对象中的最大元素个数
    .skip(int count) // 跳过指定个数的元素再操作
    .forEach(System.out::println); // 循环遍历打印
    
// 映射
集合对象.stream().map(Function<T,R> function);
集合对象.stream().flatMap(Function<T,R> function);
```

## 2-5 Optional类（理解）

# 3 面试题

## 3-1 简述Lambda表达式的特点

### 	①能省则省

### 	②数据类型可省、()可省、{}可省、return关键字可省

## 3-2 简述什么是Stream对象

### 	并非传统意义上的IO流，更像是数据传输的“管道”，通过提供指定API，完成对元素序列中元素操作的一门技术

## 3-3 简述你知道的代码优化方式有哪些？是怎样进行优化的？

### 	①在导包时，使用指定类代替*编写 -->>> 一定程度上提高代码执行效率

### 	②Lambda表达式 -->>> 简化方法参数的传递

### 	③Stream对象API -->>> 简述对于元素序列的操作

# ===========================

# Stream对象API

# 1、汇总

```java
public class StreamObjectAPITest {

    //汇总——转换操作：将stream对象操作完成的数据转换成指定类型的集合存储
    @Test
    public void testAPI1() {
        //将Stream对象转换成List集合
        List<String> streamToList = Stream.of("zz", "ll", "hh", "zz").collect(Collectors.toList());
        System.out.println("StreamToList =" + streamToList);
        //拓展：转换成List集合的某个具体实现类
        List<String> StreamToLinked = Stream.of("zz", "ll", "hh").collect(Collectors.toCollection(LinkedList::new));

        //将Stream对象转换成Set集合——自动去重
        Set<String> StreamToSet = Stream.of("zz", "ll", "hh", "zz").collect(Collectors.toSet());
        System.out.println("streamToSet =" + StreamToSet);
        //拓展：转换Set集合的某个个具体实现类
        TreeSet<String> StreamOfTreeset = Stream.of("zz", "ll", "hh", "zz").collect(Collectors.toCollection(TreeSet::new));
        /*
         * 将Stream对象换成双列集合？
         * 将stream中的某个数据，即作为Map集合中的KEY，同时作为Map集合中的VALUE
         * */
        Stream.of("zz", "ll", "hh", "zz")
                .distinct()
                .collect(Collectors.toMap(key -> key, value -> value))
                .forEach((key, value) -> System.out.println(key + "::" + value));
    }


    //汇总——统计
    @Test
    public void testAPI2() {
        List<Integer> nums = Arrays.asList(-7, 8, 5, -4, 2);
        Integer sum = nums.stream().collect(Collectors.summingInt(num -> num));
        System.out.println("sum = " + sum);
        Double avg = nums.stream().collect(Collectors.averagingInt(num -> num));
        System.out.println("avg = " + avg);
        Optional<Integer> max = nums.stream().collect(Collectors.maxBy(Comparator.comparing(num -> num)));
        System.out.println("max = " + max.get());
        Optional<Integer> min = nums.stream().collect(Collectors.maxBy(Comparator.comparingInt(num -> num)));
        System.out.println("min = " + min.get());
    }

    private static ArrayList<Student> students;

    static {
        students = new ArrayList<>();
        students.add(new Student(1L, "张玲慧", 100.0, "太开心了,摸鱼一天挣100"));
        students.add(new Student(2L, "张金", 1.0, "张金吃屁"));
        students.add(new Student(3L, "张磊", 180.0, "阿 阿 阿"));
        students.add(new Student(4L, "胡仁坤", 200.0, "又是破碎的一天"));
        students.add(new Student(5L, "张秋磊", 100.0, "我讨厌吃盒饭"));
        students.add(new Student(6L, "张金", 1.0, "又是张金吃屁"));

    }


    /*
     * 汇总 => 分组操作：根据用户条件，划分小组
     *   姓名          id
     *   KEY<String>  VALUE<list<Order>>
     * */
    @Test
    public void testAPI3() {
        Map<String, List<Student>> map = students.stream().collect(Collectors.groupingBy(Student::getShuo));
        System.out.println("map = " + map);
        /*
         * map = {
         *   又是破碎的一天=[Student(id=4, name=胡仁坤, salary=200.0, shuo=又是破碎的一天)],
         *   又是张金吃屁=[Student(id=6, name=张金, salary=1.0, shuo=又是张金吃屁)],
         *   张金吃屁=[Student(id=2, name=张金, salary=1.0, shuo=张金吃屁)],
         *   太开心了,摸鱼一天挣100=[Student(id=1, name=张玲慧, salary=100.0, shuo=太开心了,摸鱼一天挣100)],
         *   阿 阿 阿=[Student(id=3, name=张磊, salary=180.0, shuo=阿 阿 阿)],
         *   我讨厌吃盒饭=[Student(id=5, name=张秋磊, salary=100.0, shuo=我讨厌吃盒饭)]}
         * */
    }


    /*
     * 汇总——分区操作
     *   概述：根据用户传递的条件，进行两个区域的操作，一个满足条件的区域，一个不满足条件的区域
     *   根据大家的薪资以 > 10元分区
     *   判断结果           学生信息
     *   true            1，3，4，5
     *   false           2,6
     * KEY<Boolean>  VALUE<list<Order>>
     * */
    @Test
    public void testAPI4() {
        Map<Boolean, List<Student>> map = students.stream().collect(Collectors.partitioningBy(student -> student.getSalary() > 10));
        System.out.println("map = " + map);
        /*
         *map = {
         *      false=[Student(id=2, name=张金, salary=1.0, shuo=张金吃屁),
         *            Student(id=6, name=张金, salary=1.0, shuo=又是张金吃屁)],
         *      true=[Student(id=1, name=张玲慧, salary=100.0, shuo=太开心了,摸鱼一天挣100),
         *            Student(id=3, name=张磊, salary=180.0, shuo=阿 阿 阿),
         *            Student(id=4, name=胡仁坤, salary=200.0, shuo=又是破碎的一天),
         *            Student(id=5, name=张秋磊, salary=100.0, shuo=我讨厌吃盒饭)]}
         * */
    }
}
```

# 2、并行和并发

## 面试题

## 简述什么是并行？什么是并发？

### 1、并行：多核操作，随即执行，适用于数据量较大的操作，通过parallelStream方法实现

### 2、并发：单核操作，按照顺序执行，适用于数据量较小的操作，通过stream方法实现

## 实际开发中，如果需要转换，并发转并行

#### *<u>四*一百米，和别人一起跑，单核=>多核</u>*

```java
public class StreamAndParallelTest {
    @Test
    public void test() {
        List<Integer> nums = Arrays.asList(8, 2, -6, 9, 2);
        System.out.println("👇并发流（Stream 方法）👇");
        nums.stream().forEach(System.out::println);
        System.out.println("👇并行流（parallelStream 方法）👇");
        nums.parallelStream().forEach(System.out::println);
        System.out.println("==========");
        //👇将数组中的每个元素并行地打印出来
        Arrays.stream(new int[]{0, 7, 3, 8, -7}).parallel().forEach(System.out::println);
        //Arrays.stream(new int[]{0, 7, 3, 8, -7})整数数组转换为一个IntStream（整数流）
        //parallel()将原本的串行流转换为并行流
        //forEach(System.out::println)流中的每个元素，都会调用并打印
    }
    /*
     * 并发更安全，比较慢 => 单核
     * 并行不安全，比较快 => 多核
     * */
}
```



# 影响并行操作的因素

## 数据量大=>用并行

### 1、数据量的大小

### 2、数据源的结构：即存储数据的方式，已知后端存储数据最好的格式List集合和ArrayList集合

### 3、数据的状态：拆装箱，拆箱要大于装箱（JDK5版本以后改善的）

### 4、计算机核的数量：并不是越多越好

### 5、降低单元处理的开销



# 二阶段强化总结：

## 1、掌握项目中三个文件夹的创建

#### lib——导入jar文件

#### resource——编写配置文件

#### test——编写JUnitd单元测试

## 2、会导入jar文件

## 3、会使用JUnit进行单元测试

## 4、掌握.properties文件的定义解析！！！

## 5、掌握XML文件的定义

## 6、掌握工具类的封装思想

## 7、掌握ClassLoader类、Class类、Stream类、多线程创建方式

## 8、会使用反射机制操作无参构造器

## 9、会自定义注解+注解传参

## 10、Lambda表达式

### 	改造的前提：必须是一个函数式的接口、函数式接口（有且仅有一个抽象方法）

### 	Lambda表达式的特点

### 	四个常用函数式的接口

## 11、Stream对象

### 	理解什么是Stream对象？

### 	重点掌握两组API——筛选和切片

### 	理解并行和并发

## 12、从反射机制开始的面试题

# -------------------------------------------------

# 数据库  [查询——mysql -uroot -p]

## 问题：没有学习数据库之前，使用什么技术存储数据？

### 	①数据：媒体数据 和 文本数据

### 	②存储：媒体数据一般存储在硬件设备上；文本数据更多存储在文件中（常用）



## 回顾Java中存储数据的技术

### 	①变量：一个数据存储空间的表示

### 	②数组：存储一组相同数据类型的”容器“

### 	③集合：存储一组任意引用数据类型的”容器“

### 	④配置文件：按照执行的格式进行数据存储

### 	⑤Optional类：只能存储0个或1个元素





## 上述技术的不足

### 	①上述技术都是由Java语言提供（Java语言本身也不是用来做数据存储的语言），运行在JVM虚拟机中，当虚拟机结束，则数据消失，即不能进行持久化的存储

### 	②操作数据比较麻烦



# 为了避免上述不足，提出数据库技术



# 概述：

## 字面含义：存储数据的仓库

## 专业含义：可进行持久化存储的可掉电式技术



# 持久化存储（理解）

## 持久化存储：将数据存储到可掉电式的设备中，例如硬盘

## 非持久化存储：将数据存储到某个技术的临时空间中，当空间消失时数据就消失，例如运行内存





# MySql数据库核心

# 数据库怎样实现持久化存储？

## 	通过指定的应用程序（Navicat可视化应用软件），和指定的编程语言（SQL语句）将指定的数据存储到计算机磁盘中，以.sql文件格式进行存储





# 数据库分类

## 关系型数据库：通过表建立数据与数据之间的关系，操作系统磁盘（二、三阶段）

## 非关系型数据库：通过在运行内存中创建存储空间，存储数据，让用户操作（四、五、六阶段）





# 数据库常见名词解析

## DB -- DataBase：数据库

## DBMS -- DataBase Manager System：数据库管理系统（常用）

## DBAS -- DataBase Application System：数据库应用系统





# MySQL数据库

# 江湖地位：目前市场上使用频率最高的一种关系型数据库

## 关系型数据库：

### 	①MySQL数据库：主学，中型数据库，隶属于Oracle公司

### 	②Oracle数据库：大型数据库，隶属于Oracle公司

### 	③DB2数据库：大型数据库，隶属于IBM公司

### 	④SQL Server：微软，小型数据库，收费

### 	⑤Access：微软，小型数据库，收费

### 	⑥SQLite：个人开发，收费

## 存储数据的核心对象：表 -->>> 都会以<u>*.sql文件*</u>存储在磁盘中

## 所有关系型数据库统一使用SQL语句进行操作；但是不同的数据库还有自己的”方言“





# MySQL原生服务的安装 + Navicat软件的安装 + 破解（参考安装文档）

## 	注意！！！！所有的软件安装

### 		①都不建议安装的系统盘符下

### 		②安装路径千万不要出现中文、特殊符号

### 		③学习阶段可以使用软件的最新版本；但是实际开中，会使用比最新版本低2~3个段位





# MySQL常见指令（命令，了解）

## 登录MySQL指令

![image-20240723145620529](E:\JAVA58\二阶段\0723\img\image-20240723145620529.png)

## 查看当前连接下的所有数据库

![image-20240723150041232](E:\JAVA58\二阶段\0723\img\image-20240723150041232.png)

## 创建数据库

![image-20240723150343540](E:\JAVA58\二阶段\0723\img\image-20240723150343540.png)

## 删除数据库

![image-20240723150532517](E:\JAVA58\二阶段\0723\img\image-20240723150532517.png)

## 使用指定的数据库 + 展示当前数据库下的所有表

![image-20240723150748044](E:\JAVA58\二阶段\0723\img\image-20240723150748044.png)

## 查看表的设计结构（必须会）

### 	①适用场景：当导入其他人定义好的表时，需要查看表设计结构

### 	②指令展示

![image-20240723151327511](E:\JAVA58\二阶段\0723\img\image-20240723151327511.png)

## 退出指令

![image-20240723152720438](E:\JAVA58\二阶段\0723\img\image-20240723152720438.png)

# 表（理解）

## 江湖地位：是所有关系型数据库存储数据的核心对象

## 组成

![image-20240723153701795](E:\JAVA58\二阶段\0723\img\image-20240723153701795.png)



## ORM对象映射关系

### 	①表和JavaBean的映射关系

### 	②如何映射？

| 表       | JavaBean             |
| -------- | -------------------- |
| 表名     | 类名                 |
| 列名     | 成员字段             |
| 一行数据 | 指定类的某个具体对象 |



# 1、理解存储引擎

## 作用：影响数据库存储数据的性能

## MySQL中支持的存储引擎

![image-20240723154327105](E:\JAVA58\二阶段\0723\img\image-20240723154327105.png)

## 面试题：简述MySQL数据库中常用的存储引擎？优缺点是什么？

| 常见存储引擎                         | 优点           | 不足             |
| ------------------------------------ | -------------- | ---------------- |
| InnoDB（MySQL5.x开始为默认存储引擎） | 支持外键和事务 | 查询速度较慢     |
| MyISAM                               | 查询速度快     | 不支持事务和外键 |

## 外键：多用于多表查询，但是实际开发中很少使用

## 事务：当数据进行增删改操作时，一旦操作过程中出现问题，如果使用事务，会还原数据；否则数据丢失（事务处理会在JDBC部分讲解）；实际开发中，金融类项目更多应用



# MySQL中支持的数据类型和与Java对比（重点记忆）

## MySQL数据库中只支持数值型、字符型、日期型

## MySQL中数据类型和Java对比

| MySQL数据类型 | Java数据类型               |
| ------------- | -------------------------- |
| INT(n)        | Integer/int                |
| BIGINT(n)     | Long/long                  |
| BIT           | Boolean/boolean            |
| DOUBLE(m,n)   | Double/double              |
| DECIMAL       | BigDecimal                 |
| CHAR(n)       | String                     |
| VARCHAR(n)    | StringBuffer/StringBuilder |
| TEXT          | String                     |
| DATE          | java.util.Date             |
| DATETIME      | java.util.Date             |
| TIMESTEMP     | Calendar                   |



## 数值型

### 	①整数型：INT、BIGINT、BIT，其中n表示当前整数存储的最大位数

### 	②小数型：DOUBLE、DECIMAL，其中m表示小数的总位数，n表示小数部分有几位



## 字符型

### 	①CHAR：定长字符，n表示字符储存的个数，如果实际存储个数小于n，则也会按照n的值开辟存储空间

### 	②VARCHAR：可变长度字符，n表示字符储存的个数，如果实际存储个数小于n，则会按照实际存储个数开辟存储空间；最大值为255；对应Java中StringBuffer/StringBuilder，由于StringBuffer/StringBuilder操作缓冲区，最后需要归还堆栈内存，即以String类型存储，因此实际开中，VARCAHR类型映射到JavaBean中，直接与String类型对应



## 日期型：实际开发中操作频率最低的数据类型

### 	①DATE：操作年月日

### 	②DATETIME：操作年月日时分秒

### 	③TIMESTEMP：时间戳，操作毫秒数

### 	④当想要向表中存储日期数据时，值一定定义在""中，按照指定格式存储



# 2、理解单表操作



# 3、SQL语句编写操作



# 4、重点掌握MySql中支持的数据类型和与java中数据类型的对比



# 5、DDL



# 6、DML





# 原生JDBC

# 使用JDBC操作数据库，完成CRUD操作



# data access object思想（DAO）



# 理解事物管理



# 理解连接池（7大池）



# 重点MyBatis框架的基础应用——死都要会，每天练