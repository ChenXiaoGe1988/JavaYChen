## MyBatis逆向工程的应用

### Mybatis generator

> 可以根据数据库生成model、mapper.xml、mapper接口和Example，通常情况下的单表查询不用再手写mapper。

#### 项目搭建

##### 在pom.xml中添加相关依赖

```xml
<!--日志我们使用logback-->
<dependency>
  	<groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
<!-- MyBatis 生成器 -->
<dependency>
	<groupId>org.mybatis.generator</groupId>
 	<artifactId>mybatis-generator-core</artifactId>
    <version>1.3.7</version>
</dependency>
<!-- Mybatis必备依赖包 -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
<!-- mysql驱动依赖包，连接mysql必备-->
<dependency>
	<groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>
```

##### 编写logback.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <!--定义日志文件输出地址-->
    <property name="LOG_ERROR_HOME" value="error"/>
    <property name="LOG_INFO_HOME" value="info"/>

    <!--通过appender标签指定日志的收集策略，我们会定义三个收集策略：控制台输出、普通信息文件输出、错误信息文件输出-->
    <!--name属性指定appender命名-->
    <!--class属性指定输出策略，通常有两种，控制台输出和文件输出，文件输出就是将日志进行一个持久化-->

    <!--控制台输出-->
    <appender name="CONSOLE_LOG" class="ch.qos.logback.core.ConsoleAppender">
        <!--使用该标签下的标签指定日志输出格式-->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--
            %p:输出优先级，即DEBUG,INFO,WARN,ERROR,FATAL
            %r:输出自应用启动到输出该日志讯息所耗费的毫秒数
            %t:输出产生该日志事件的线程名
            %f:输出日志讯息所属的类别的类别名
            %c:输出日志讯息所属的类的全名
            %d:输出日志时间点的日期或时间，指定格式的方式： %d{yyyy-MM-dd HH:mm:ss}
            %l:输出日志事件的发生位置，即输出日志讯息的语句在他所在类别的第几行。
            %m:输出代码中指定的讯息，如log(message)中的message
            %n:输出一个换行符号
            -->
            <pattern>%red(%d{yyyy-MM-dd HH:mm:ss.SSS}) %yellow([%-5p]) %highlight([%t]) %boldMagenta([%C]) %green([%L]) %m%n</pattern>
        </encoder>
    </appender>

    <!--普通信息文件输出-->
    <appender name="INFO_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--通过使用该标签指定过滤策略-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!--标签指定过滤的类型-->
            <level>ERROR</level>
            <onMatch>DENY</onMatch>
            <onMismatch>ACCEPT</onMismatch>
        </filter>

        <encoder>
            <!--标签指定日志输出格式-->
            <pattern>[%d{yyyy-MM-dd' 'HH:mm:ss.SSS}] [%C] [%t] [%L] [%-5p] %m%n</pattern>
        </encoder>

        <!--标签指定收集策略，比如基于时间进行收集-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--标签指定生成日志保存地址，通过这样配置已经实现了分类分天收集日志的目标了-->
            <fileNamePattern>${LOG_INFO_HOME}//%d.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!--错误信息文件输出-->
    <appender name="ERROR_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
        <encoder>
            <pattern>[%d{yyyy-MM-dd' 'HH:mm:ss.SSS}] [%C] [%t] [%L] [%-5p] %m%n</pattern>
        </encoder>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_ERROR_HOME}//%d.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!--用来设置某一个包或具体的某一个类的日志打印级别-->
    <logger name="com.ckb.ssm.mapper" level="DEBUG"/>

    <!--必填标签，用来指定最基础的日志输出级别-->
    <root level="info">
        <!--添加append-->
        <appender-ref ref="CONSOLE_LOG"/>
        <appender-ref ref="INFO_LOG"/>
        <appender-ref ref="ERROR_LOG"/>
    </root>
</configuration>
```

##### Mybatis generator 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <properties resource="database.properties"/>
    <context id="MySqlContext" targetRuntime="MyBatis3" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>
        <property name="javaFileEncoding" value="UTF-8"/>
        <!-- 为模型生成序列化方法-->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
        <!-- 为生成的Java模型创建一个toString方法 -->
        <plugin type="org.mybatis.generator.plugins.ToStringPlugin"/>
        <!--生成mapper.xml时覆盖原文件-->
        <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin" />
        <!--是否在代码中显示注释-->
        <commentGenerator>
            <property name="suppressDate" value="true" />
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--可以自定义生成model的代码注释-->
        <!--<commentGenerator type="com.ckb.ssm.mbg.CommentGenerator">
            &lt;!&ndash; 是否去除自动生成的注释 true：是 ： false:否 &ndash;&gt;
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="true"/>
            <property name="addRemarkComments" value="true"/>
        </commentGenerator>-->
        <!--配置数据库连接-->
        <jdbcConnection driverClass="${jdbc.driver}"
                        connectionURL="${jdbc.url}"
                        userId="${jdbc.username}"
                        password="${jdbc.password}">
            <!--解决mysql驱动升级到8.0后不生成指定数据库代码的问题-->
            <property name="nullCatalogMeansCurrent" value="true" />
        </jdbcConnection>
        <!--指定生成javaBean的路径-->
        <javaModelGenerator targetPackage="com.ckb.ssm.entity" targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="true" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <!--指定生成mapper.xml的路径-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

        <!--指定生成mapper接口的的路径-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.ckb.ssm.mapper"
                             targetProject="src/main/java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!--指定数据库表
            tableName为对应的数据库表
            domainObjectName是要生成的实体类
            如果想要mapper配置文件加入sql的where条件查询, 可以将enableCountByExample等设为true
            这样就会生成一个对应domainObjectName的Example类
            searchString="^Tbl" 表示去掉表前缀
         -->

        <!--<table  tableName="tbl_dic_type" domainObjectName="DicType"
                enableCountByExample="false" enableUpdateByExample="false"
                enableDeleteByExample="false" enableSelectByExample="false"
                selectByExampleQueryId="false">
            <property name="useActualColumnNames" value="true" />
            &lt;!&ndash;<generatedKey column="id" sqlStatement="MySql" identity="true"/>&ndash;&gt;
            <domainObjectRenamingRule searchString="^Tbl" replaceString=""/>
        </table>-->
    </context>
</generatorConfiguration>
```

##### 编写运行类,一般情况下不允许运行该类，加了个时间进行限制，防止意外运行该类导致原先pojo类、mapper接口、xml映射文件上的注释被覆盖没了。

```java
package com.ckb.ssm.mbg;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

/**
 * 用于生产MBG的代码
 * @author ychen
 */
public class MybatisGenerator {
    public static void main(String[] args) throws Exception {
        {
            String today = "2020-11-27";

            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
            Date now = sdf.parse(today);
            Date d = new Date();

            if (d.getTime() > now.getTime() + 1000 * 60 * 60 * 24) {
                System.err.println("――――――未成成功运行――――――");
                System.err.println("――――――未成成功运行――――――");
                System.err.println("本程序具有破坏作用，应该只运行一次，如果必须要再运行，需要修改today变量为今天，如:" + sdf.format(new Date()));
                return;
            }

            if (false)
                return;
            //MBG 执行过程中的警告信息
            List<String> warnings = new ArrayList<String>();
            //当生成的代码重复时，覆盖原代码
            boolean overwrite = true;
            //读取我们的 MBG 配置文件
            InputStream is = MybatisGenerator.class.getClassLoader().getResource("generatorConfig.xml").openStream();
            ConfigurationParser cp = new ConfigurationParser(warnings);
            Configuration config = cp.parseConfiguration(is);
            is.close();
            DefaultShellCallback callback = new DefaultShellCallback(overwrite);
            //创建 MBG
            MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
            //执行生成代码
            myBatisGenerator.generate(null);
            //输出警告信息
            for (String warning : warnings) {
                System.out.println(warning);
            }
        }
    }
}
```

