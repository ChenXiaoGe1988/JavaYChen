---
title: Mybatis入门
date: 2020-11-05 16:27:35
tags:
- MyBatis
- Java持久层框架
- 学习笔记
categories:
- MyBatis

## Mybatis是什么?
- Mybatis是一个基于Java的持久层框架
- Mybatis既不像Hibernate那样灵活度差,也不像jdbc开发式的诸多麻烦,Mybatis就像找到两者之间的g点一样,它可以使用XMl或注解来映射原生类型,接口和Java的POJO为数据库中的记录,可谓是业界必会呀!

## Mybatis快速入门
### *1.在pom.xml中导入依赖*
```xml
<dependencies>
    <!-- 1.引入mybatis依赖 -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.1.1</version>
    </dependency>
    <!-- 2.log4j 依赖 -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
    <!-- 3.mysql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.39</version>
    </dependency>
  </dependencies>
```
### *2.配置日志文件log4j.properties*
```properties
### 设置 ###
log4j.rootLogger = info,stdout
### 输出信息到控制台 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
## mapper包映射路径 ###
log4j.logger.com.ckb.mybatis.mapper = debug
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n
```
### *3.数据库创建Student表以及实体类Student的创建*
- sql语句
```sql
-- 使用数据库
use ckb_student_service;
-- 创建学生表
CREATE TABLE `student` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '学生主键ID',
  `name` varchar(255) DEFAULT NULL COMMENT '学生姓名',
  `number` int(11) DEFAULT NULL COMMENT '学号',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
-- 插入初始化数据
INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (12, 'JiaCen', 8888);
INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (13, 'YChen', 6666);
INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (14, 'ckb', 17436204);
```
- Student实体类(我的放在com.ckb.mybatis.entity包下)
```java
/**
 * 学生实体类
 */
public class Student {
    /*学生主键ID*/
    private String id;
    /*学生姓名*/
    private String name;
    /*学号*/
    private String number;
    public Student() {
    }
    public Student(String id, String name, String number) {
        this.id = id;
        this.name = name;
        this.number = number;
    }
    public String getId() {
        return id;
    }
    public void setId(String id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getNumber() {
        return number;
    }
    public void setNumber(String number) {
        this.number = number;
    }
    @Override
    public String toString() {
        return "Student{" +
                "学生ID='" + id + '\'' +
                ", 学生姓名='" + name + '\'' +
                ", 学生编号='" + number + '\'' +
                "}\n";
    }
}
```

### *4.创建Mybatis配置文件--mybatis-config.xml*
- 可以放在Resources目录下
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis配置-->
<configuration>
    <!--加载类路径下的属性文件-->
    <properties resource="db.properties"/>
    <!--全局环境配置-->
    <environments default="development">
        <environment id="development">
            <!--事物-->
            <transactionManager type="JDBC"/>
            <!--配置数据源-->
            <dataSource type="POOLED">
                <!--数据库驱动-->
                <property name="driver" value="${mysql.driver}"/>
                <!--数据库路径-->  
                <property name="url" value="${mysql.url}"/>
                <!--账号-->
                <property name="username" value="${mysql.username}"/>
                <!--密码-->
                <property name="password" value="${mysql.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入自定义mapper.xml-->
    <mappers>
        <mapper resource="mapper/StudentMapper.xml"/>
    </mappers>
</configuration>
```
### *5.db.properties文件的配置*
- 同样放在Resources目录下
```properties
mysql.driver=com.mysql.jdbc.Driver
mysql.user=root
mysql.password=password
#1.连接云服务器
#mysql.url=jdbc:mysql://xx.xx.xxx.xx:3306/database?useUnicode=true&characterEncoding=utf-8&useSSL=false
#2.连接本地
mysql.url=jdbc:mysql://127.0.0.1:3306/database?useUnicode=true&characterEncoding=utf-8&useSSL=false
```
### *6.编写工具类是否测试获取到连接*
- 工具类MybatisUtil放在了com.ckb.mybatis.utils下
```java
package com.ckb.mybatis.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.*;
import java.io.IOException;
import java.io.Reader;
import java.sql.Connection;

/**
 * 工具类
 */
public class MybatisUtil {
    private static ThreadLocal<SqlSession> threadLocal = new ThreadLocal<SqlSession>();
    private static SqlSessionFactory sqlSessionFactory;
    /**
     * 构造方法私有 -- 禁止外界通过new方法创建
     */
    private MybatisUtil() {
    }
    
    /**
     * 加载mybatis-config.xml配置文件
     */
    static {
        try {
            Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        } catch (IOException e) {
            e.printStackTrace();
            throw  new RuntimeException(e);
        }
    }

    /**
     * 获取SqlSession
     * @return
     */
    public static SqlSession getSqlSession() {
        //从当前线程中获取SqlSession对象
        SqlSession sqlSession = threadLocal.get();
        //如果为空
        if (sqlSession == null) {
            sqlSession = sqlSessionFactory.openSession();
            //将SqlSession与当前线程绑定在一起
            threadLocal.set(sqlSession);
        }
        return sqlSession;
    }

    /**
     * 资源关闭
     */
    public static void closeSqlSession() {
        //从当前线程中获取SqlSession对象
        SqlSession sqlSession = threadLocal.get();
        //如果SqlSession对象非空
        if (sqlSession != null) {
            //关闭SqlSession对象
            sqlSession.close();
            //分开当前线程与SqlSession的关系,目的是让GC尽早回收
            threadLocal.remove();
        }
    }

    /**
     * 测试是否连接成功
     * @param args
     * @throws IOException
     */
    public static void main(String[] args) throws IOException {
        Connection conn = MybatisUtil.getSqlSession().getConnection();
        System.out.println(conn != null ? "数据库连接成功!" : "数据库连接失败!");
    }
}
```
### *7.配置实体与表的映射关系*
- StudentMapper.xml(位于Resources目录下的mapper文件夹中)
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--对应StudentMapper接口路径-->
<mapper namespace="com.ckb.mybatis.mapper.StudentMapper">

</mapper>
```
- 将配置文件和映射文件关联起来(在mybatis-config.xml文件中)
```xml
<!--引入自定义mapper.xml-->
    <mappers>
        <mapper resource="mapper/StudentMapper.xml"/>
    </mappers>
```
- 测试是否连接到数据库
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/720d8060e91a404cb76fe28d316878fd~tplv-k3u1fbpfcp-watermark.image)

### *8.编写DAO*
- StudentDao.java
```java
package com.ckb.dao;

import com.ckb.mybatis.entity.Student;
import com.ckb.mybatis.utils.MybatisUtil;
import org.apache.ibatis.session.SqlSession;
import java.io.IOException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 实现对学生表的增删改查
 */
public class StudentDao {

    /**
     * 1.增加学生
     * @throws IOException
     */
    public void addStudent()throws IOException {
        //得到连接对象
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try{
            //映射文件的命名空间.SQL片段的ID，就可以调用对应的映射文件中的SQL
            sqlSession.insert("addStudent");
            sqlSession.commit();
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            MybatisUtil.closeSqlSession();//资源关闭
        }
    }
    /**
     * 2.查询学生
     */
    public void findAll() throws IOException {
        //获取连接对象
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try{
            //使用SqlSession查询
            List<Student> getStudent = sqlSession.selectList("findAll");
            for (Student student : getStudent) {
                System.out.println(student.toString());
            }
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 3.根据ID查询学生
     * @param id 学生主键id
     * @return 学生对象
     * @throws Exception
     */
    public Student findById(int id) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try{
            return sqlSession.selectOne("findById",id);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 4.根据ID删除学生
     * @param id
     * @throws Exception
     */
    public void delete(int id ) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try {
            sqlSession.delete("delete",id);
            sqlSession.commit();
        } catch (Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally{
            MybatisUtil.closeSqlSession();
        }
    }


    /**
     * 5.修改学生信息
     * @param student
     * @throws IOException
     */
    public void update(Student student) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try{
            sqlSession.update("update", student);
            sqlSession.commit();
        } catch (Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally{
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 6.分页查询
     * @param start 
     * @param end
     * @return
     */
    public List<Student> pagination(int start, int end)throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try {
            Map<String, Object> map = new HashMap<>();
            map.put("start",start);
            map.put("end", end);
            return sqlSession.selectList("pagination",map);
        } catch (Exception e) {
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally {
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 7.动态查询
     * @param name
     * @param number
     * @return
     * @throws Exception
     */
    public List<Student> findByCondition(String name, String number) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try{
            /**
             * 由于我们的参数超过了两个，而方法中只有一个Object参数收集
             * 因此我们使用Map集合来装载我们的参数
             */
            Map<String, Object> map = new HashMap();
            map.put("name", name);
            map.put("number", number);
            return sqlSession.selectList("findByCondition", map);
        } catch (Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally{
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 8.动态更新
     * @param id
     * @param name
     * @param number
     * @throws IOException
     */
    public void updateByCondition(String id, String name, String number) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try {
            Map<String, Object> map = new HashMap<>();
            map.put("id", id);
            map.put("name", name);
            map.put("number", number);
            sqlSession.selectList("updateByCondition",map);
            sqlSession.commit();
        } catch (Exception e) {
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally {
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 9.动态删除
     * @param ids
     * @throws IOException
     */
    public void deleteByCondition(int... ids) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try {
            sqlSession.delete("deleteByCondition",ids);
            sqlSession.commit();
        } catch (Exception e) {
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally {
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 10.动态插入
     * @param student
     * @throws Exception
     */
    public void insertByConditions (Student student) throws IOException {
        SqlSession sqlSession = MybatisUtil.getSqlSession();
        try{
            sqlSession.insert("insertByConditions", student);
            sqlSession.commit();
        } catch (Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        } finally{
            MybatisUtil.closeSqlSession();
        }
    }

    /**
     * 测试
     * @param args
     * @throws IOException
     */
    public static void main(String[] args) throws IOException {
        StudentDao studentDao = new StudentDao();
        /**
         * 1.增加学生
         */
        //studentDao.addStudent();
        /**
         * 2.查询所有学生信息
         */
        //studentDao.findAll();//查询所有学生信息
        /**
         * 3.根据ID查询学生
         */
        //Student student = studentDao.findById(2);
        //System.out.println(student.toString());
        /**
         * 4.根据ID删除学生
         */
        //studentDao.delete(3);//根据id删除学生

        /**
         * 5.修改学生信息
         */
        //Student student = studentDao.findById(2);
        //student.setName("掘金玩家");
        //student.setNumber("10000");
        //studentDao.update(student);

        /**
         * 6.分页查询
         */
        /*List<Student> students = studentDao.pagination(0,6);
        for (Student student : students) {
            System.out.println(student.toString());
        }*/

        /**
         * 7.动态查询
         */
        /*List<Student> students = studentDao.findByCondition(null,"996");
        for (Student student : students) {
            System.out.println(student.toString());
        }*/

        /**
         * 8.动态更新
         */
        //studentDao.updateByCondition("10", "ChenXiaoGe", "996");

        /**
         * 9.动态删除编号为2和10的学生
         */
        //studentDao.deleteByCondition(2,10);

        /**
         * 10.动态插入
         */
        studentDao.insertByConditions(new Student("11",null, null));
        studentDao.insertByConditions(new Student("22","ChenJiaCen", null));
        studentDao.insertByConditions(new Student("33",null, "1"));
    }
}

```
- StudentMapper.xml(完整文件)
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--对应StudentMapper接口路径-->
<mapper namespace="com.ckb.mybatis.mapper.StudentMapper">

    <!-- 1.增加学生 -->
    <insert id="addStudent" parameterType="com.ckb.mybatis.entity.Student" >
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (4, 'Java3y', 30000);
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (5, 'JavaGuide', 40000);
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (6, 'CodeSheep', 10000);
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (7, '技术胖', 600);
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (8, '敖丙', 500);
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (9, 'Jack', 800);
--         INSERT INTO `ckb_student_service`.`student`(`id`, `name`, `number`) VALUES (10, 'ChenXiaoGe', 1900);
    </insert>

    <!-- 2.查询学生所有信息 -->
    <select id="findAll" resultType="com.ckb.mybatis.entity.Student">
    select * from student;
    </select>

    <!-- 3.根据Id查询学生信息 -->
    <select id="findById" parameterType="int" resultType="com.ckb.mybatis.entity.Student">
        select * from student where id = #{id};
    </select>

    <!-- 4.根据ID删除学生 -->
    <delete id="delete" parameterType="int" >
    delete from student where id = #{id};
    </delete>

    <!-- 5.修改学生信息 -->
    <update id="update" parameterType="com.ckb.mybatis.entity.Student">
    update student set name = #{name} ,number = #{number} where id = #{id};
    </update>

    <!-- 6.分页查询 -->
    <select id="pagination" parameterType="map"  resultType="com.ckb.mybatis.entity.Student">
    select * from student limit #{start},#{end};
    </select>

    <!-- 7.动态查询 -->
    <select id="findByCondition" parameterType="map" resultType="com.ckb.mybatis.entity.Student">
    select * from student
    <where>
        <if test="name!=null">
            and name = #{name}
        </if>
        <if test="number!=null">
            and number > #{number}
        </if>
    </where>
    </select>

    <!-- 8.动态更新 -->
    <update id="updateByCondition" parameterType="map">
    update student
    <set>
        <if test="name!=null">
            name = #{name},
        </if>
        <if test="number!=null">
            number = #{number},
        </if>
    </set>
    where id = #{id}
    </update>

    <!-- 9.动态删除 -->
    <delete id="deleteByCondition" parameterType="int" >
        <!-- foreach用于迭代数组元素
                 open表示开始符号
                 close表示结束符合
                 separator表示元素间的分隔符
                 item表示迭代的数组，属性值可以任意，但提倡与方法的数组名相同
                 #{ids}表示数组中的每个元素值
             -->
        delete from student where id in
        <foreach collection="array" open="(" close=")" separator="," item="ids">
            #{ids}
        </foreach>
    </delete>
    <!--SQL片段默认是不帮我们自动生成合适的SQL，因此需要我们自己手动除去逗号-->
    <sql id="key">
        <trim suffixOverrides=",">
            <if test="id!=null">
                id,
            </if>
            <if test="id!=null">
                name,
            </if>
            <if test="id!=null">
                number,
            </if>
        </trim>
    </sql>
    <sql id="value">
        <trim suffixOverrides=",">
            <if test="id!=null">
                #{id},
            </if>
            <if test="id!=null">
                #{name},
            </if>
            <if test="id!=null">
                #{number},
            </if>
        </trim>
    </sql>
    <!-- 10.动态插入 -->
    <!--动态插入-->
    <insert id="insertByConditions" parameterType="com.ckb.mybatis.entity.Student">
        insert into student (<include refid="key"/>) values
        (<include refid="value"/>)
    </insert>
</mapper>
```
## Mybatis小结!
- *Mybatis的事务默认是开启的，需要我们手动去提交事务*
- *Mybatis的SQL语句是需要手写的，在程序中通过映射文件的命名空间.sql语句的id来进行调用!* 
- *在Mybatis中，增删改查都是需要我们自己写SQL语句的，然后在程序中调用即可了。SQL由于是我们自己写的，于是就相对Hibernate灵活一些。*
- *如果需要传入多个参数的话，那么我们一般在映射文件中用Map来接收*
- *由于我们在开发中会经常用到条件查询，在之前，我们是使用查询助手来帮我们完成对SQL的拼接的。而Mybatis的话，我们是自己手写SQL代码的。*



