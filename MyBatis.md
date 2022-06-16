### 一、MyBatis框架部署

---

#### 1.1 mybatis-config.xml 配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

<!--  environments  标签里用于配置数据库连接信息-->
    <environments default="mysql">

        <environment id="mysql">
<!--  transactionManager用于配置数据库管理方式-->
            <transactionManager type="JDBC"></transactionManager>
<!-- dataSource用于配置数据库连接信息 -->
            <dataSource type="POOLED">
             <property name="driver" value="com.mysql.jdbc.Driver"/>
             <property name="url" value="jdbc:mysql://localhost:3306/db_mtl?characterEncoding=utf8"/>
             <property name="username" value="root"/>
             <property name="password" value="gph"/>
            </dataSource>
        </environment>
         
    </environments>
    
</configuration>


```

#### 1.2 pom.xml配置依赖

```xml
<dependencies>
        <!-- MySql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <!-- MyBatis框架 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>
</dependencies>
```

| ![image-20220601205257920](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220601205257920.png) * 添加后需要在Maven中(屏幕右边)加载Maven项目 |
| ------------------------------------------------------------ |

### 二、框架使用

---

#### 2.1 创建数据表

| tb_students                                                  |
| ------------------------------------------------------------ |
| create table tb_students(<br/>    stu_id int PRIMARY key auto_increment,<br/>		stu_num char(5) not null unique,<br/>		stu_name VARCHAR(20) not null,<br/>		stu_gender char(1) not null,<br/>		stu_age int not null<br/>); |

#### 2.2 创建实体类

| Students                                                     |
| ------------------------------------------------------------ |
| ![image-20220602085631185](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602085631185.png) |

##### 2.2.1  导入lombok依赖

>```xml
>        <dependency>
>            <groupId>org.projectlombok</groupId>
>            <artifactId>lombok</artifactId>
>            <version>1.18.12</version>
>                        <scope>provided</scope>
>        </dependency>
>```

| 利用lombok注解来生成构造器，toString()方法,   @Data注解用来生成get()和set方法() |
| ------------------------------------------------------------ |
| ![image-20220602133216234](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602133216234.png) |

#### 2.3 创建StudentDao,定义操作方法

| StudentDao.java                                              |
| ------------------------------------------------------------ |
| ![image-20220602134444509](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602134444509.png) |



#### 2.4 创建DAO接口的映射文件

* 在`resource`目录下，新建名为`mappers`文件夹

* 在`mappers`中新建名为`StudentMapper.xml`的映射文件(根据模板创建)

  | 在StudentMapper.xml中实现接口                                |
  | ------------------------------------------------------------ |
  | ![image-20220603094843414](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603094843414.png) |

  * 将映射文件添加到主配置文件(mybatis-config.xml)中

    | ![image-20220602141046676](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602141046676.png) |
    | ------------------------------------------------------------ |

    

#### 2.5 创建测试类,StudentDaoTest

```java
 try {
      //加载Mybatis配置文件
    InputStream  is = Resources.getResourceAsStream("mybatis-config.xml");
      SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
      // 会话工厂
      SqlSessionFactory factory = builder.build(is);
      // sqlSession对象代表数据库连接与Mybatis操作对象
      // 会话(连接)
      SqlSession sqlSession = factory.openSession();
      StudentDao studentDao = sqlSession.getMapper(StudentDao.class);
      Student student = new Student(0, "1236", "小明", "男", 12);
      int i = studentDao.insertStudent(student);
      //需要手动提交
      sqlSession.commit();
      System.out.println(i);
    } catch (IOException e) {
      e.printStackTrace();
    }
```

##### 2.5.1   !!!注意事项

| 在StudentMapper.xml中需要将namespace的地址中间用   .   分隔(包名.类名) |
| ------------------------------------------------------------ |
| ![image-20220602171240911](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602171240911.png) |

### 三、MyBatis的CRUD操作

---

> 案例:对学生信息的增删改查

#### 3.1 添加操作(上述案例已经完成)

 **补充:**

* 在 StudentMapper.xml中的**insert**标签中新增两个属性

| useGeneratedKeys | 设置添加操作是否需要回填生成的主键         |
| ---------------- | ------------------------------------------ |
| **keyProperty**  | **设置回填的主键赋值到参数对象的哪个属性** |

>![image-20220604110839485](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604110839485.png)

* 进行测试:

  | ![image-20220604110957587](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604110957587.png) |
  | ------------------------------------------------------------ |
  | ![image-20220604111056383](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604111056383.png) |

  

#### 3.2 删除操作

* 在StudentDAO中定义删除方法

  | StudentDAO                                                   |
  | ------------------------------------------------------------ |
  | ![image-20220602183349642](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602183349642.png) |

  

* 在StudentMapper.xml中增加删除配置

  | StudentMapper.xml                                            |
  | ------------------------------------------------------------ |
  | ![image-20220602183522250](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602183522250.png) |

  

* 接着在mybatis-config.xml 中增加配置

  | mybatis-config.xml                                           |
  | ------------------------------------------------------------ |
  | ![image-20220602183700941](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602183700941.png) |

* 在测试类中新增方法

  | StudentDAOTest                                               |
  | ------------------------------------------------------------ |
  | ![image-20220602184036471](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602184036471.png) |

  

| 删除前                                                       | 删除后                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220602184129326](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602184129326.png) | ![image-20220602184151973](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602184151973.png) |



#### 3.3 修改操作

>根据学生学号，修改其他字段信息

* 在StudentDAO中定义新方法

  | StudentDAO                                                   |
  | ------------------------------------------------------------ |
  | ![image-20220602192442272](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602192442272.png) |

  

* 在StudentMapper.xml中增加修改操作配置

  | StudentMapper.xml                                            |
  | ------------------------------------------------------------ |
  | ![image-20220602193601067](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602193601067.png) |

* 在StudentDAOTest中创建测试方法

  | StudentDAOTest                                               |
  | ------------------------------------------------------------ |
  | ![image-20220602194354609](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602194354609.png) |

  

>sqlSession.commit()方法详解

  

| 1.从数据库的层面上来讲，其实这个主要看你用什么“存储引擎” |
| -------------------------------------------------------- |
| 2.使用myIsam引擎，不需要commit                           |

#### 3.4 查询操作

##### 3.4.1 方法一

* 在StudentDAO中新增方法

| StudentDAO                                                   |
| ------------------------------------------------------------ |
| ![image-20220602205722914](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602205722914.png) |

* 在StudentMapper.xml中新增查询配置

  | StudentMapper.xml                                            |
  | ------------------------------------------------------------ |
  | ![image-20220602205931427](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602205931427.png) |

  ```xml
      <!-- resultType 指定查询结果封装的对象实体类-->
      <!-- resultSets  指定当前操作返回的集合类型(可省略)-->
         <select id="selectStudents" resultType="dto.Student" resultSets="java.util.List">
             select stu_id stuId,stu_num stuNum,stu_name stuName,stu_gender stuGender,stu_age stuAge from tb_students;
         </select>
  ```

* 在StudentDAOTest中新增测试方法

  | StudentDAOTest                                               |
  | ------------------------------------------------------------ |
  | ![image-20220602210135296](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602210135296.png) |
  | **结果返回**                                                 |
  | ![image-20220602210218843](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602210218843.png) |

  

##### 3.4.2 方法二(推荐)

* 在StudentMapper.xml中新增标签

  ​	

  | StudentMapper.xml                                            |
  | ------------------------------------------------------------ |
  | ![image-20220602211812411](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220602211812411.png) |

  

* 其他操作与方法一相同

#### 3.5 查询一条记录

>根据学号查询一个学生信息

| StudentDAO                                                   |
| ------------------------------------------------------------ |
| ![image-20220603193029485](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603193029485.png) |

* 在StudentMapper.xml中增加新配置信息

  | StudentMapper.xml                                            |
  | ------------------------------------------------------------ |
  | ![image-20220603193150385](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603193150385.png) |
  | ![image-20220603193211519](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603193211519.png) |

  

* 在StudentDAOTest中新增测试方法

  

| StudentDAOTest                                               |
| ------------------------------------------------------------ |
| ![image-20220603193315011](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603193315011.png) |
| **以下为结果**                                               |
| ![image-20220603193402272](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603193402272.png) |

#### 3.6 查询总记录数

| StudentDAO                                                   |
| ------------------------------------------------------------ |
| ![image-20220603194637327](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603194637327.png) |



* 在StudentMapper.xml中增加新配置信息

  

| StudentMapper.xml                                            |
| ------------------------------------------------------------ |
| 因为返回的结果是**整型**，所以需要指定**resultType**类型     |
| ![image-20220603194952750](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603194952750.png) |

* 在StudentDAOTest中新增测试方法

| StudentDAOTest                                               |
| ------------------------------------------------------------ |
| ![image-20220603195113733](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603195113733.png) |
| 结果如下:                                                    |
| ![image-20220603195142529](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603195142529.png) |

#### 3.7 查询操作---多参数查询

>分页查询
>
>* 参数start ,  pageSize

##### 3.7.1 MyBatis进行操作:

###### 1.如果操作方法只有一个简单类型或者字符串类型的参数

   >**1.如果操作方法只有一个简单类型或者字符串类型的参数，在Mapper配置中可以直接通过#{key}  (key可任意)来直接获取**
   >
   >**例:**![image-20220603212646548](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603212646548.png)
   >
   >**并且在StudentMapper.xml中，我修改了#{}中的参数(而且是任意取的值)**
   >
   >![image-20220603212846280](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603212846280.png)
   >
   >**结果仍然可返回!**
   >
   >![image-20220603212959178](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603212959178.png)
   >
   >![image-20220603213010072](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603213010072.png)

###### 2.如果操作方法有一个对象类型的参数

>2.如果操作方法有一个对象类型的参数，在Mapper配置中可以直接通过#{attrName}获取对象的指定属性值(attrName必须是参数对象的属性)
>
>![image-20220603213222285](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603213222285.png)
>
>![image-20220603213301809](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603213301809.png)

###### 3.操作方法有一个Map类型的参数

>3.如果操作方法有一个Map类型的参数，在Mapper配置中可以直接通过#{key}获取key对应的value
>
>* 在StudentDAO中定义新方法
>
>![image-20220603214430062](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603214430062.png)
>
>* 在StudentMapper.xml 中新增配置sql  (**start为从第几条记录开始 ,  pageSize为显示几条记录**)
>
>  ![image-20220603214559873](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603214559873.png)resultMap如下:
>
>  ![image-20220603214643185](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603214643185.png)
>
>  * 在StudentDAOTest中新增测试方法
>
>  ![image-20220603215052065](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603215052065.png)
>
>  **结果如下:**
>
>  ![image-20220603215251494](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603215251494.png)

###### 4.操作方法有多个参数

>* 在StudentDAO中定义新方法
>
>  ![image-20220603220103699](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603220103699.png)
>
>  * 在StudentMapper.xml中增加新配置sql  
>
>    ![image-20220603220910410](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603220910410.png)
>
>    **注:** 如果使用**#{arg参数位置}**格式,则参数位置**默认从0**开始,如果使用**#{param参数位置}** 则参数位置**默认从1**开始
>
>    * 在StudentDAOTest中增加测试方法
>
>    ![image-20220603220518123](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603220518123.png)
>
>    **结果如下**
>
>    ![image-20220603220536603](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603220536603.png)

**补充:**

>* 还可以在StudentDAO中定义方法时增加注解(包:import org.apache.ibatis.annotations.Param;)
>
>![image-20220603221608903](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603221608903.png)
>
>* 此时就可以在StudentMapper.xml中使用别名**#{别名}**
>
>  ![image-20220603221703995](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603221703995.png)
>
>  * 测试
>
>    ![image-20220603221731277](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603221731277.png)
>
>    * 结果如下:
>
>      ![image-20220603221751066](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220603221751066.png)

### 四、CRUD-工具类封装

---

```java
public class MyBatisUtil {
 private   static  SqlSessionFactory factory = null;
 //线程锁
 private   static final ThreadLocal<SqlSession> local = new ThreadLocal<>();
   //加载配置文件并生成工厂
    static {
        try {
            //加载MyBatis配置文件
            InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
             //会话工厂
            factory = new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //返回SqlSession对象
    public static SqlSession getSqlSession(){
        SqlSession sqlSession = local.get();
        if (sqlSession == null){
            //打开会话
            sqlSession = factory.openSession();
            //将sqlSession设置到ThreadLocal中,保证多个线程对共享会话的安全访问
            local.set(sqlSession);
        }
        return sqlSession;
    }

    //<T extends ?>规定了T的上限  支持?类及?的子类
    //<T super ?>规定了T的下限   支持?类及?的父类
    public  static <T extends Object> T getMapper(Class<T> c){
        T dao = getSqlSession().getMapper(c);
        return dao;
    }

}
```

**测试:**

| StudentDAOTest.java                                          |
| ------------------------------------------------------------ |
| ![image-20220604181622953](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604181622953.png) |
| **结果如下:**                                                |
| ![image-20220604181652329](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604181652329.png) |

### 五、事务管理

---

>SqlSession对象
>
>* getMapper(DAO.class);   获取Mapper  (DAO接口的实例)
>* 事务管理

#### 5.1 手动提交事务

* ``sqlSession.commit();`` 提交事务
* ``sqlSession.rollback();`` 事务回滚

```java
public void insertStudent() {
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    try {
      //1.利用工具类获取DAO对象
      StudentDao studentDao = MyBatisUtil.getMapper(StudentDao.class);
      Student student = new Student(0, "12359", "小连", "男", 21);
      studentDao.insertStudent(student);
      //2.手动提交
      sqlSession.commit();
      System.out.println(student);
    }catch (Exception e){
      //3.当操作出现异常,调用rollback()进行回滚
      sqlSession.rollback();
    }
  }
```

#### 5.2 自动提交事务

> //通过SqlSessionFactory调用openSession()方法获取SqlSession对象时,可以通过参数设置事务是否自动提交
>    //1.如果参数设置为true,表示自动提交事务,factory.openSession(true);
>    //2.如果参数为false,或者不设置参数,表示手动提交,factory.openSession();/factory.openSession(false);

```java
public class MyBatisUtil {
 private   static  SqlSessionFactory factory = null;
 //线程锁
 private   static final ThreadLocal<SqlSession> local = new ThreadLocal<>();
   //加载配置文件并生成工厂
    static {
        try {
            //加载MyBatis配置文件
            InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
             //会话工厂
            factory = new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
     //手动事务提交
    public static SqlSession getSession(){
        //设置手动提交
        SqlSession sqlSession = getSqlSession(false);
        return sqlSession;
    }

    //返回SqlSession对象
    public static SqlSession getSqlSession(boolean isAutoCommit){
        SqlSession sqlSession = local.get();
        if (sqlSession == null){
        //打开会话
       //通过SqlSessionFactory调用openSession()方法获取SqlSession对象时,可以通过参数设置事务是否自动提交
       //1.如果参数设置为true,表示自动提交事务,factory.openSession(true);
       //2.如果参数为false,或者不设置参数,表示手动提交,factory.openSession();/factory.openSession(false);
            sqlSession = factory.openSession(isAutoCommit);
            //将sqlSession设置到ThreadLocal中,保证多个线程对共享会话的安全访问
            local.set(sqlSession);
        }
        return sqlSession;
    }
    //自动提交
    public  static <T extends Object> T getMapper(Class<T> c){
        T dao = getSqlSession(true).getMapper(c);
        return dao;
    }

}
```

### 六、MyBatis主配置文件

---

>mybatis-config.xml是MyBatis框架的主配置文件,主要配置MyBatis数据源及属性信息

#### 6.1 properties标签

>用于设置键值对,或者加载属性文件

* 在resources目录下创建``jdbc.properties``文件,配置键值对如下:

  ```properties
  mysql_driver=com.mysql.jdbc.Driver
  mysql_url=jdbc:mysql://localhost:3306/db_mtl?characterEncoding=utf8
  mysql_username=root
  mysql_password=gph
  ```

* 在mybatis-config.xml中通过``properties`` 标签引用``jdbc.properties``文件;引入之后,在配置environment时可以直接使用jdbc.properties的key获取对应的value

  

| mybatis-config.xml                                           |
| ------------------------------------------------------------ |
| ![image-20220604201812814](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604201812814.png) |

#### 6.2 settings标签(需要设置在properties标签后)

```xml
<!--    设置mybatis的属性-->
    <settings>
<!--        启用二级缓存-->
        <setting name="cacheEnabled" value="true"/>
<!--        启动延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
    </settings>
```

>二级缓存 MyBatis的二级缓存是Application级别的缓存，它可以**提高对数据库查询的效率，以提高应用的性能**。
>
> 延迟加载又叫按需查询（懒加载），mybatis支持延迟加载，我们希望**一次性把常用的级联数据通过sql直接查询出来，而对于那些不常用的的级联数据不要取出，而是等待要用的时候才取出**，这些不常用的级联数据可以采用延迟加载的功能。

#### 6.3 typeAlias标签

```xml
<!--    typeAliases标签用于给实体类取别名,在映射文件中可以直接使用别名来替代实体类的全限定名-->
    <typeAliases>
        <typeAlias type="dto.Student" alias="Student"></typeAlias>
    </typeAliases>
```

| mybatis-config.xml                                           |
| ------------------------------------------------------------ |
| ![image-20220604203635672](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604203635672.png) |
| **StudentMapper.xml**                                        |
| ![image-20220604203651224](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604203651224.png) |

#### 6.4 plugins标签

| mybatis-config.xml                                           |
| ------------------------------------------------------------ |
| ![image-20220604204124591](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604204124591.png) |

#### 6.5 transactionManager标签

| ![image-20220604204438440](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604204438440.png) |
| ------------------------------------------------------------ |

#### 6.6 dataSource标签

| POOLED:使用连接池,UNPOOLED:不使用连接池                      |
| ------------------------------------------------------------ |
| ![image-20220604204734929](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604204734929.png) |

#### 6.7 mappers标签

| ![image-20220604204833599](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220604204833599.png) |
| ------------------------------------------------------------ |

### 七、映射文件

---

#### 7.1 MyBatis初始化

![image-20220605124251916](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605124251916.png)

#### 7.2 mapper根标签

>```xml
><!-- mapper文件相当于DAO接口的实现类,namespace要指定实现的接口的全限定名(包名+类名) -->
><mapper namespace="dao.StudentDao">
></mapper>
>```

#### 7.3 insert标签

>声明添加操作(sql : insert...)  
>
>**常用属性:**
>
>**id**属性,绑定对应DAO接口中的方法
>
>**parameterType**属性,用于指定接口中对应方法的参数类型(可省略)
>
>**useGeneratedKeys**属性,设置添加操作是否需要回填生成的主键
>
>**keyProperty**属性,指定回填的id设置到参数对象中的哪个属性
>
>**timeout**属性,设置此操作的超时时间,如果不设置，则一直等待

```xml
<!--    id要和方法名一致-->
<insert id="insertStudent"  parameterType="Student" useGeneratedKeys="true" keyProperty="stuId"timeout="3000">
          insert into tb_students(stu_num,stu_name,stu_gender,stu_age)
                      values(#{stuNum},#{stuName},#{stuGender},#{stuAge});
        </insert>
```



**主键回填**(推荐``方式一``)

![image-20220605153654791](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605153654791.png)

#### 7.4 delete标签

>声明删除操作
>
>id属性,指定绑定方法的方法名

![image-20220605153956542](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605153956542.png)

#### 7.5 update标签

>声明修改操作
>
>id属性,指定绑定方法的方法名

![image-20220605154055551](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605154055551.png)

#### 7.6 select标签

>声明查询操作
>
>* **id**属性,指定绑定方法的方法名
>* **parameterType**属性,用于指定接口中对应方法的参数类型(可省略)
>* **resultType**属性,指定当前 sql 返回数据封装的对象类型 (实体类)
>* **restultMap**属性,指定从数据表到实体类之间的对应关系
>* **useCache**属性,指定此查询是否需要缓存(**二级缓存**)
>* **timeout**属性,设置超时时间

#### 7.7 resultMap标签

```xml
<!--    resultMap表示定义实体类与数据表的映射关系(ORM)-->
    <resultMap id="listStudents" type="Student">
        <id column="stu_id" property="stuId"></id>
        <result column="stu_num" property="stuNum"></result>
        <result column="stu_name" property="stuName"></result>
        <result column="stu_gender" property="stuGender"></result>
        <result column="stu_age" property="stuAge"></result>
    </resultMap>
```

#### 7.8 cache标签

>设置当前DAO进行数据库操作时的缓存属性设置

![image-20220605155937328](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605155937328.png)

#### 7.9 sql和include

>SQL片段,通过sql标签的**id**属性和include标签的**refid**属性进行关联

```xml
    <sql id="studentMes">
        stu_id,stu_num,stu_name,stu_gender,stu_age
    </sql>
<!-- resultMap用于引用一个实体的映射关系,当配置了resultMap之后,resultType就可以省略-->
    <select id="selectStudents" resultMap="listStudents">
        select <include refid="studentMes"/> from tb_students;
    </select>
```

### 八、分页插件

---

>分页插件是一个独立于MyBatis框架之外的第三方插件
>
>PageHelper

#### 8.1 添加分页插件的依赖

>pom.xml中配置

```xml
<!-- PageHelper分页插件 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.10</version>
</dependency>

```

#### 8.2 配置插件

>在mybatis的主配置文件``mybatis-config.xml``中通过``plugins``标签进行配置

```xml
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
    </plugins>
```

#### 8.3 分页示例

>对学生信息进行分页查询(**查询第二页,四条数据**)

```java
    //1.通过映射得到studentDao对象
    StudentDao studentDao = MyBatisUtil.getMapper(StudentDao.class);
    //2.分页设置一定要在查询之前
   PageHelper.startPage(2, 4);
    //3.查询所有学生
    List<Student> students = studentDao.selectStudents();
    //4.初始化
    PageInfo<Student> pageInfo = new PageInfo<>(students);
    //5.得到集合进行显示,pageInfo 中就包含了数据及分页信息
    List<Student> list = pageInfo.getList();
    for (Student student:list){
      System.out.println(student);
    }
```

 **带条件分页**

| StudentDAO.java定义新方法``根据性别查询``                    |
| ------------------------------------------------------------ |
| ![image-20220605171850012](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605171850012.png) |
| **StudentMapper.xml**                                        |
| ![image-20220605172008533](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605172008533.png) |

### 九、关联映射

----

#### 9.1 实体关系

>实体----数据实体, 实体关系指的就是数据与数据之间的关系
>
>例如: 用户和角色

实体关系分为以下四种:

**1.  一对一关联**

实例: 人和身份证,学生和学生证

数据表关系:

* **主键关联**

 ![image-20220605173839724](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605173839724.png)



* **唯一外键关联**

![image-20220605174055645](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220605174055645.png)



**一对多关联、多对一关联**

实例:

* 一对多:   班级和学生、类别和商品、楼栋和房屋
* 多对一:   学生和班级、商品和类别、房屋和楼栋

数据表关系:

* 在多的一端添加外键   和   一的一端进行关联



**2.  多对多关联**

实例:用户和角色,角色和权限,房屋和业主,学生和社团,订单和商品  

数据表关系:  建立第三张关系表,添加两个外键分别与两张表主键进行关联

**用户**(user_id)      **用户角色表**(uid,rid)         **角色**(role_id)



#### 9.2 创建项目,  部署MyBatis框架

* 创建web项目  (基于maven)

| pom.xml                                                      |
| ------------------------------------------------------------ |
| ![image-20220606142919276](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606142919276.png) |
| ![image-20220606142944987](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606142944987.png) |

```xml
<dependencies>
<!--        添加web依赖-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
        </dependency>
</dependencies>

<!--    创建web目录必须增加此配置-->
    <packaging>war</packaging>
```



| main目录下                                                   |
| ------------------------------------------------------------ |
| 在main目录下新建目录``webapp``, 然后在``webapp``目录下新建目录``WEB-INF``目录,  在``WEB-INF``目录新建配置文件 ``web.xml`` |
| ![image-20220606143130076](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606143130076.png) |



**web.xml模板**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">
</web-app>
```



* 部署MyBatis框架

```xml
        <!-- mybatis依赖 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version>
        </dependency>

        <!-- mysql依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
```

* 配置文件

| mybatis-config.xml                                           |
| ------------------------------------------------------------ |
| ![image-20220606155702587](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606155702587.png) |

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <properties resource="jdbc.properties"></properties>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${root}"/>
                <property name="password" value="${gph}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
       
```



| jdbc.properties                                              |
| ------------------------------------------------------------ |
| ![image-20220606155939071](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606155939071.png) |

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/db_mtl?characterEncoding=utf8
username=root
password=gph
```



* 工具类

```java
package utils;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

public class MyBatisUtil {
    private static SqlSessionFactory factory;
    private static final ThreadLocal<SqlSession> tl = new ThreadLocal<>();

    //加载工厂
    static {
        try {
            InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
            factory = new SqlSessionFactoryBuilder().build(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession(boolean isAutoCommit){
        SqlSession sqlSession = tl.get();
        if (sqlSession == null){
            sqlSession = factory.openSession(isAutoCommit);
            tl.set(sqlSession);
        }
        return sqlSession;
    }

   //手动提交
    public static SqlSession getSqlSession(){
        SqlSession sqlSession = getSqlSession(false);
        return sqlSession;
    }

    //获得对应的映射实体类DAO
    public static <T extends Object> T getMapper(Class<T> t){
        SqlSession sqlSession = getSqlSession(true);
        T dao = sqlSession.getMapper(t);
        return dao;
    }
    
}

```

#### 9.3 一对一关联查询

>实例:用户---详情

##### 9.3.1  创建数据表

```sql
-- 用户信息表
create table users(
  user_id int primary key auto_increment,
  user_name varchar(32) unique not null default'',
 user_pwd varchar(20) not null,
 user_real_name varchar(32) not null,
 user_img varchar(60) not null 
);

-- 用户详情表
create table details(
  detail_id int primary key auto_increment,
  user_addr varchar(60) not null,
  user_tel char(11) not null,
  user_desc varchar(200),
  user_id int unique not null
);
```

##### 9.3.2 创建实体类

* User

```java
package dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

@ToString
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
  private  int  userId;
  private String   userName;
  private String  userPwd;
  private String   userRealName;
  private String   userImg;

}

```

* Detail

```java
package dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

@NoArgsConstructor
@AllArgsConstructor
@Data
@ToString
public class Detail {
  private int  detailId;
  private String   userAddr;
  private String   userTel;
  private String   userDesc;
  private int   userId;
}

```

##### 9.3.3 添加操作

| insertUser                                                   |
| ------------------------------------------------------------ |
| ![image-20220606193359264](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606193359264.png) |



##### 9.3.4 一对一关联查询

>在查询用户的同时关联查询与之对应的详情 

* 实体

| User                                                         | Detail                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220606190009205](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606190009205.png) | ![image-20220606190020476](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606190020476.png) |

**映射文件**

* 连接查询

| UserMapper.xml                                               |
| ------------------------------------------------------------ |
| ![image-20220606190642767](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606190642767.png) |

* 子查询

| DetailMapper.xml                                             |
| ------------------------------------------------------------ |
| ![image-20220606192208955](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606192208955.png) |
| **UserMapper.xml**                                           |
| ![image-20220606192326349](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220606192326349.png) |

#### 9.4 一对多关联查询

>案例:   班级----学生

##### 9.4.1 创建数据表

![image-20220607163310836](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607163310836.png)





##### 9.4.2  创建实体类

![image-20220607163601222](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607163601222.png)

* 定义接口方法

![image-20220607163806068](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607163806068.png)



##### 9.4.3 一对多关联查询

>当查询一个班级的时候,要关联查询这个班级下面的所有学生

**连接查询**

| 连接查询映射配置                                             |
| ------------------------------------------------------------ |
| ![image-20220607163723231](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607163723231.png) |



**子查询**

| 子查询映射配置                                               |
| ------------------------------------------------------------ |
| ![image-20220607164514557](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607164514557.png) |
| ![image-20220607164604820](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607164604820.png) |

#### 9.5 多对一关联

   >实例 ： 学生---班级
   >
   >当查询一个学生的时候，关联查询这个学生对应的班级信息

##### 9.5.1 创建实体类

| Student                                                      | Clazz                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220607165202796](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607165202796.png) | ![image-20220607165242749](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607165242749.png) |



##### 9.5.2 关联查询

**连接查询**

| 连接查询映射配置                                             |
| ------------------------------------------------------------ |
| ![image-20220607165741394](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607165741394.png) |

**子查询**

| 子查询映射配置                                               |
| ------------------------------------------------------------ |
| ![image-20220607170148499](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607170148499.png) |
| ![image-20220607170235647](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607170235647.png) |



#### 9.6 多对多关联

> 案例:  学生 --- 课程



##### 9.6.1 创建数据表

![image-20220607195413305](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607195413305.png)





##### 9.6.2 关联查询

>查询学生时，同时查询学生选择的课程

| Student                                                      | Course                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220607195821551](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607195821551.png) | ![image-20220607195901925](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607195901925.png) |







>根据课程号查询课程时，同时查询选择了这门课程的学生

| Student                                                      | Course                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20220607200015142](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607200015142.png) | ![image-20220607200027046](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220607200027046.png) |



| 连接查询关联配置                                             |
| ------------------------------------------------------------ |
| ![image-20220609194002695](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220609194002695.png) |

| 子查询                                                       |
| ------------------------------------------------------------ |
| ![image-20220609194518114](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220609194518114.png) |
| ![image-20220609194611819](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220609194611819.png) |



### 十、动态SQL

---

>电商平台:淘宝，京东
>
>筛选:  品牌、价格



>用户的筛选条件不同,我们完成筛选执行的SQL也不一样;  可以通过穷举来一 一 完成不同条件的筛选,
>
> 但是思路过于复杂
>
>**MyBatis**提供了**动态SQL**的配置方式来实现多条件查询

#### 10.1 什么是动态 SQL？

>根据查询条件动态完成SQL拼接

#### 10.2  动态SQL使用案例

>案例:  心仪对象搜索

##### 10.2.1 创建数据表

```sql
create table members(
  member_id int primary key auto_increment,
  member_nick varchar(32) not null,
  member_gender char(2) not null,
  member_age int not null,
  member_city varchar(32) not null
);
```

##### 10.2.2 创建实体类

```java
@ToString
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Member {
  private int     memberId;
  private String   memberNick;
  private String   memberGender;
  private int     memberAge;
  private String   memberCity;
}
```



##### 10.2.3 创建DAO接口

>在 DAO接口中定义一个多条件查询的方法 

```java
public interface MemberDAO {
    //在多条件查询中，如果查询条件不确定，可以直接使用HashMap作为参数
    public List<Member> searchMember(HashMap<String,Object> param);
}
    //新建一个Condition类,并存入可能出现的属性
    public List<Member> searchMemberByCondition(MemberSearchCondition params);
```

``MemberSearchCondition``类,并设置``get()``和``set()``方法

```java
@Data
public class MemberSearchCondition {
    private String gender;
    private Integer minAge;
    private Integer maxAge;
    private String city;
}
```

#### 10.3 if 标签的用法

``memberMapper.xml``

```java
<resultMap id="MemberMap" type="Member">
     <id column="member_id" property="memberId"/>
        <result column="member_nick" property="memberNick"/>
        <result column="member_gender" property="memberGender"/>
        <result column="member_age" property="memberAge"/>
        <result column="member_city" property="memberCity"/>
    </resultMap>
    <select id="searchMemberByCondition" resultMap="MemberMap" >
         select  member_id,member_nick,member_gender,member_age,member_city
                  from members
                where 1=1
                      <if test="gender != null">
                       and  member_gender=#{gender}
                      </if>
                      <if test="minAge != null">
                       and member_age &gt;= #{minAge}
                      </if>
                      <if test="maxAge != null">
                        and member_age &lt;= #{maxAge}
                      </if>
                    <if test="city != null">
                        and member_city = #{city}
                    </if>
    </select>
```

#### 10.4 **where** 标签的用法

| ![image-20220612104451671](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220612104451671.png) |
| ------------------------------------------------------------ |

#### 10.5 **trim** 标签的用法

(``prefix``为前缀,``prefixOverrides``为当第一个条件前有**and**的时候默认省略该语句,``suffix``为后缀)

![image-20220612105242625](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220612105242625.png)

#### 10.6 **foreach**标签的用法

![image-20220612112858766](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220612112858766.png)



### 十一、模糊查询

---

>根据昵称查询会员信息(模糊匹配 like )

#### 11.1 模糊查询的实现

#####   11.1.1 DAO

```java
public interface MemberDAO {
 //模糊查询-根据昵称查询用户信息
 //模糊查询需要使用 ${} 取值,与sql进行拼接
 //在使用 ${} 即使只有一个参数也需要使用注解来声明参数的key @Param("参数名") 非String类型可以不用声明
    public List<Member> searchMemberByNick(@Param("keyWord") String keyWord);
}
```

##### 11.1.2 映射文件

```xml
    
<select id="searchMemberByNick"  resultMap="MemberMap">
        select  member_id,member_nick,member_gender,member_age,member_city
          from members
           where  member_nick like '%${keyWord}%'
    </select>
```

##### 11.1.3 测试

```java
    @Test
    public void searchMemberByNick() {
        MemberDAO memberDAO = MyBatisUtil.getMapper(MemberDAO.class);
        List<Member> members = memberDAO.searchMemberByNick("丽");
        for (Member member:members){
            System.out.println(member);
        }
    }
```

**结果**

![image-20220612184704665](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220612184704665.png)

#### 11.2 #{} 和 ${} 的区别

* ``${key}`` 表示获取参数，先获取参数的值拼接到SQL语句中,在编译执行SQL语句；可能引起 SQL注入问题

* ``#{key}`` 表示获取参数,  先完成SQL编译(预编译) , 预编译之后再将获取的参数设置到SQL中，可以避免SQL注入问题



### 十二、Mybatis日志配置

---

>MyBatis作为一个封装好的ORM框架，其运行过程我们没办法跟踪，为了让开发者了解MyBatis执行流程及每个执行步骤所完成的工作，MyBatis框架本身继承了log4j 日志框架，对运行的过程进行了跟踪记录，我们只需对MyBatis进行相关的日志配置，就可以看到MyBatis运行过程中的日志信息

#### 12.1 添加日志框架依赖

  ```xml
    <!-- log4j依赖(MyBatis框架日志依赖) -->
          <dependency>
              <groupId>log4j</groupId>
              <artifactId>log4j</artifactId>
              <version>1.2.12</version>
          </dependency>
  ```



#### 12.2 添加日志配置文件

* 在 resources 目录下创建名为``log4j.properties``文件
* 在``log4j.properties``文件配置日志输出的方式

  ```properties
  ## 声明日志的输出级别及输出方式
  log4j.rootLogger=DEBUG,stdout
  log4j.logger.org.mybatis.example.BlogMapper=TRACE
  log4j.appender.stdout=org.apache.log4j.ConsoleAppender
  log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
  ## 定义日志的打印格式  %t 表示线程名称  %5p表示输出日志级别 %msg为日志信息
  log4j.appender.stdout.layout.ConversionPattern=[%t] %5p - %msg  %m%n
  ```

#### 12.3 日志信息的级别

>在使用日志框架输出日志信息的时候，会根据日志信息的重要程度分为5个级别

| 级别  | 说明           |
| ----- | -------------- |
| DEBUG | 输出调试信息   |
| INFO  | 输出提示信息   |
| WARN  | 输出警告信息   |
| ERROR | 一般性错误信息 |
| FATAL | 致命性错误信息 |



### 十三、配置数据库连接池-整合Druid

---

>MyBatis作为一个ORM框架，在进行数据库操作时是需要和数据库连接的，MyBatis支持基于数据库连接池的连接方式创建方式。
>
>当我们配置MyBatis数据源时，只要配置了dataSource标签的type属性值为POOLED时，就可以使MyBatis内置的连接池管理连接。
>
>如果我们想要使用第三方的数据库连接池，则需要自行配置。

#### 13.1 常见的连接池

* DBCP
* C3P0
* Druid : 性能比较好，提供了比较便捷的监控系统
* Hikari : 性能最好

| 功能            | dbcp              | druid              | c3p0                               | tomcat-jdbc | HikariCP                           |
| --------------- | ----------------- | ------------------ | ---------------------------------- | ----------- | ---------------------------------- |
| 是否支持PSCache | 是                | 是                 | 是                                 | 否          | 否                                 |
| 监控            | jmx               | jmx/log/http       | jmx,log                            | jmx         | jmx                                |
| 扩展性          | 弱                | 好                 | 弱                                 | 弱          | 弱                                 |
| sql拦截及解析   | 无                | 支持               | 无                                 | 无          | 无                                 |
| 代码            | 简单              | 中等               | 复杂                               | 简单        | 简单                               |
| 更新时间        | 2015.8.6          | 2015.10.10         | 2015.12.09                         |             | 2015.12.3                          |
| 特点            | 依赖于common-pool | 阿里开源，功能全面 | 历史久远，代码逻辑复杂，且不易维护 |             | 优化力度大，功能简单，起源于boneCP |

#### 13.2 添加Druid依赖

```xml
         <!-- Druid依赖 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.5</version>
        </dependency>
```



#### 13.3 创建Druid连接池工厂

```java
public class DruidDataSourceFactory extends PooledDataSourceFactory {
 protected DataSource dataSource;

    public DruidDataSourceFactory() {
        //这里的dataSource为父类的fu(UnpooledDataSourceFactory)的属性
        this.dataSource = new DruidDataSource();
    }
}
```



#### 13.4 将DruidDataSourceFactory配置给MyBatis数据源

```xml
<environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
    <!--  POOLED 使用MyBatis内置的连接池实现 -->
    <!--  MyBatis需要一个连接池工厂，这个工厂可以产生数据库连接池 PooledDataSourceFactory -->
            <dataSource type="utils.DruidDataSourceFactory">
                <property name="driverClass" value="${driver}"/>
                <property name="jdbcUrl" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
</environments>
```



### 十四、MyBatis缓存

---

>MyBatis是基于JDBC的封装，使数据库操作更加便捷；MyBatis除了对JDBC操作步骤进行封装之外，也能对其性能进行优化:
>
>* 在MyBatis引入缓存机制，用于提升MyBatis的检索效率
>* 在MyBatis引入延迟加载机制，用于减少对数据库不必要的访问

#### 14.1 缓存的工作原理

>缓存: 就是存储数据的内存

![image-20220615165930694](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615165930694.png)



#### 14.2 MyBatis缓存

> MyBatis缓存分为一级缓存和二级缓存



##### 14.2.1 一级缓存

>一级缓存也叫做SqlSession级缓存，为每个SqlSession对象单独分配的缓存内存，无需手动开启可直接使用
>
>多个SqlSession的缓存不共享
>
>特性：
>
>1. 如果多次查询使用的是同一个SqlSession对象（``同一个会话``），则第一次查询之后数据会放到缓存，后续的查询则直接访问缓存中存储的数据
>2. 如果第一次查询完成之后，对查询出的对象进行修改（此修改会影响到缓存），第二次查询会直接访问缓存，造成第二次查询结果与数据库不一致
>3. 当我们进行在查询时想要跳过缓存直接查询数据库，则可以通过sqlSession.clearCache();来清楚当前SqlSession的缓存
>4. 如果第一次查询之后第二次查询之前，使用当前的SqlSession执行了修改操作，此修改操作会使第一次查询并缓存的数据失效，因此第二次查询会再次访问数据库

**测试**

![image-20220615174611826](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615174611826.png)

##### 14.2.2 两次查询与数据库数据不一致问题

![image-20220615180740945](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615180740945.png)

##### 14.2.3 二级缓存

   >二级缓存也称为SqlSessionFactory级缓存，通过同一个factory对象获取的SqlSession可以共享二级缓存
   >
   >在应用服务器中SqlSessionFactory是单例的，因此我们二级缓存可以实现全局共享
   >
   >之所以称之为“二级缓存”，是相对于“一级缓存”而言的。既然有了一级缓存，那么为什么要提供二级缓存呢？我们知道，在一级缓存中，不同session进行相同SQL查询的时候，是查询两次数据库的。显然这是一种浪费，既然SQL查询相同，就没有必要再次查库了，直接利用缓存数据即可，这种思想就是MyBatis二级缓存的初衷。
   >
   >特性：
   >
   >1. 二级缓存默认没有开启，需要在mybatis-config.xml中的setting标签开启
   >2. 二级缓存只能缓存实现序列化接口的对象

* 在mybatis-config.xml开启二级缓存

![image-20220615182324539](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615182324539.png)

* 在需要使用二级缓存的Mapper文件中配置cache标签使用功能二级缓存

```xml
<cache/>
```

* 被缓存的实体类实现序列化接口 

```java
@ToString
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Member implements Serializable {
  private int     memberId;
  private String   memberNick;
  private String   memberGender;
  private int     memberAge;
  private String   memberCity;
}
```

* 测试

![image-20220615184319759](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615184319759.png)



#### 14.3 查询操作的缓存开关

``只有select标签有useCache开关``

![image-20220615184808195](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615184808195.png)



### 十五、延迟加载

>延迟加载---如果在MyBatis开启了延迟加载，在执行了``子查询``（至少查询两次以上），默认只执行第一次查询，当``用到子查询``的查询结果时，才会``触发``子查询的执行；如果无需使用子查询结果，则子查询不会执行 

* 示例

![image-20220615191453358](C:\Users\007\AppData\Roaming\Typora\typora-user-images\image-20220615191453358.png)
