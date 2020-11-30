

### 一.CRM开发概述

#### 系统设置模块 setting操作

##### 用户模块: 登录

##### 数据字典模块信息的查询

#### 工作台 workbench

##### 市场活动模块: activity

- 点击创建按钮,打开模态窗口
- 添加操作
- 展示市场活动信息列表(条件查询+分页查询)
- 全选/反选
- 删除操作
- 市场活动详情信息页的展示
- 备注信息列表的展示
- 备注的添加,修改,删除

##### 线索模块(潜在用户模块): clue

- 点击创建按钮,打开添加操作模态窗口

##### 交易模块

##### 统计图表





### 二.知识点总结

### Ajax的几种表现形式(jquery)

##### $.ajax

- 功能齐全,使用方便,应用广泛

```js
$(function() {
    $("#djBtn").click(function() {
        /**
        * 同步和异步
        *  async : true 异步
        *  		全程两根线程,一根执行普通代码,一根执行ajax,两者相互独立
        *  async : false 同步
        *		全程一根线程,从上往下依次执行
        *	实际开发一般情况下都是使用异步请求可以有效提升用户体验
        *	在特殊需求下使用同步(如:表单验证)
        */
        $.ajax({
			url : "myServlet01.do", //访问servlet地址 
			//data : "key1=value1&key2=value2", //为后台传递的参数
			type : "get", //请求方式 get/post
    		dataType : "text", //从后台接受数据的方式 text/json
            async : true, //默认是true 即异步 false为同步
    		success : function(data) { //回调函数
        		//data
        		$("#msg").html(data);
   			 }
		})
    })
})
```

##### $.get/post

- 使用更加方便
- 保留了核心ajax功能,去除了一些扩展功能

##### $.getJSON

- 针对于json数据解析的形式



### ajax:

#### 局部刷新

#### 异步请求

### Jquery存取值的理解

#### 1.针对与value值

##### val(值):存值

##### val():取值

#### 2.针对于标签对

##### html(值):存值

##### html():取值

#### 3.针对于内容

##### text(值):存值

##### text:取值



### GET请求和POST请求的区别

#### get:取,拿

- 以查询为目的

#### post:邮递

- 以添加,修改,删除为目的

#### 如果参数涉及到安全性问题

##### 登录操作需要传递密码

- 则使用**post**方式

### JSON

#### JSON拼接练习１

##### {key : value}

##### {"str1" : "abc", "num" : 100, "success" : true}

```java
System.out.println("servlet02");
String str = "{\"str1\":\"aaa\", \"str2\":\"bbb\"}";
```

```javascript
$(function() {
    $("#djBtn").click(function() {
        /**
       	*
        */
        $.ajax({
			url : "myServlet02.do",  
			type : "get", 
    		dataType : "json", 
    		success : function(data) { 
        		alert(data); //object Object
                //以json.key的形式取得value
                alert(data.str1);
   			 }
		})
    })
})
```

```java
System.out.println("servlet03");
String s = new Student("A001","zs",23);
//{"id":"?", "name":"?", "age":?}
String str = "{\"id\":\""+s.getId()+"\", \"name\":\""+s.getName()+"\",\"age\":"+s.getAge()+"}";
PrintWriter out = resp.getWriter();
out.print(str);
out.close();
```

##### domain:领域模型

- pojo , bean , javabean, 实体类

```java
public class Student {
    private String id;
    private String name;
    private int age;
    public Student() {}
    public Student(String id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    //getter and setter
}
```

- js

```js
$(function() {
    $("#djBtn").click(function() {
        /**
       	*
        */
        $.ajax({
			url : "myServlet03.do",  
			type : "get", 
    		dataType : "json", 
    		success : function(data) { 
        		alert(data); //object Object
                alert(data.id);
                alert(data.name);
                alert(data.age);
   			 }
		})
    })
})
```

#### JSON拼接练习2

- Servlet

```java
		System.out.println("servlet03");
        Student s1 = new Student("A001","zs",23);
        Student s2 = new Student("A002","Chen",22);
        //{"id":"?", "name":"?", "age":?}
        //{"s1":{"id":"?", "name":"?", "age":?}, "s2":{"id":"?", "name":"?", "age":?}}
        //String str = "{\"id\":\""+s.getId()+"\", \"name\":\""+s.getName()+"\",\"age\":"+s.getAge()+"}";
        String str = "{\"s1\":{\"id\":\""+s1.getId()
                +"\", \"name\":\""+s1.getName()
                +"\", \"age\":"+s1.getAge()
                +"}, \"s2\":{\"id\":\""+s2.getId()
                +"\", \"name\":\""+s2.getName()
                +"\", \"age\":"+s2.getAge()+"}}";
        PrintWriter out = resp.getWriter();
        out.print(str);
        out.close();
```

- jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path +"/";
%>
<html>
<base href="<%=basePath%>">
<head>
    <title>test02</title>
    <script src="js/jquery.min.js"></script>
    <script>
        $(function() {
            $("#djBtn").click(function() {
                $.ajax({
                    url : "myServlet04.do",
                    type : "get",
                    dataType : "json",
                    success : function(data) {
                        //alert(data); //object Object
                        $("#id1").html(data.s1.id);
                        $("#name1").html(data.s1.name);
                        $("#age1").html(data.s1.age);
                        $("#id2").html(data.s2.id);
                        $("#name2").html(data.s2.name);
                        $("#age2").html(data.s2.age);
                    }
                })
            })
        })
    </script>
</head>
<body>
 <button id="djBtn">点击</button>
<br>
<br>
    学生1:<br>
    编号:<span id="id1"></span><br>
    姓名:<span id="name1"></span><br>
    年龄:<span id="age1"></span><br>
    <br>
    <br>
    学生2:<br>
    编号:<span id="id2"></span><br>
    姓名:<span id="name2"></span><br>
    年龄:<span id="age2"></span><br>
</body>
</html>
```

- 测试截图

![](/home/ychen/图片/2020-11-13 07-59-29屏幕截图.png)

![](/home/ychen/图片/2020-11-13 07-59-51屏幕截图.png)

### 前后端传值的方式

#### 前端为后端传递值,叫传参数

##### 1.url?key1=value1&key2=value2

##### 2.form name

##### 3.ajax

#####      		data:{}

##### 无论哪一种传递方式,后台一律使用下面的形式来接受参数

```java
String value = request.getParameter(key);
```

##### 特殊形式:同一个key有多个value(如:批量删除操作)?

##### xxx/xxx/delete.do?id=A0001&id=A0002&id=A0003

```java
String ids[] = request.getParameterValues("id");
```

#### 后台为前端提供数据

![](/home/ychen/图片/2020-11-12 17-49-22屏幕截图.png)

 

#### Servlet模板模式的应用

##### Servlet家族的基础结构(从上往下,依次派生)

- Servlet 
- GenericServlet
- HttpServlet
- MyServlet01

##### 模板模式: 将程序执行的流程或算法的骨架搭建出来,具体的实现方式交给方法去做!

##### 对于Servlet的创建是按照模块划分的,一个模块的由一个统一的Servlet来处理

### 例:使用Servlet实现简单增删改查

- pom.xml

```xml
<dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.2</version>
    </dependency>
  </dependencies>
```

- jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort() + path +"/";
%>
<html>
<base href="<%=basePath%>">
<head>
    <title>test05</title>
    <script src="js/jquery.min.js"></script>
</head>
<body>
 <h2>学生管理系统</h2>
<br>
<br>
<a href="/student/save.do">添加</a><br><br>
<a href="/student/update.do">修改</a><br><br>
<a href="/student/delete.do">删除</a><br><br>
<a href="/student/select.do">查询</a><br><br>
</body>
</html>
```

- web.xml注册Servlet

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
         <display-name>Archetype Created Web Application</display-name>
         <servlet>
    <servlet-name>MyServlet05</servlet-name>
    <servlet-class>com.ckb.servlet.MyServlet05</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>MyServlet05</servlet-name>
    <url-pattern>/student/save.do</url-pattern>
    <url-pattern>/student/update.do</url-pattern>
    <url-pattern>/student/delete.do</url-pattern>
    <url-pattern>/student/select.do</url-pattern>
  </servlet-mapping>
</web-app>
```

- MyServlet05.java

```java
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class MyServlet05 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //取得url-pattern
        String path = request.getServletPath();
        if ("/student/save.do".equals(path)) {
            save(request, response);
        }else if("/student/update.do".equals(path)){
            update(request, response);
        }else if ("/student/delete.do".equals(path)) {
            delete(request, response);
        }else if ("/student/select.do".equals(path)) {
            select(request, response);
        }

    }
    private void save(HttpServletRequest request, HttpServletResponse response){
        System.out.println("执行添加操作");
    }
    private void update(HttpServletRequest request, HttpServletResponse response){
        System.out.println("执行修改操作");
    }
    private void delete(HttpServletRequest request, HttpServletResponse response){
        System.out.println("执行删除操作");
    }
    private void select(HttpServletRequest request, HttpServletResponse response){
        System.out.println("执行查询操作");
    }
}
```

- 测试截图

![](/home/ychen/图片/2020-11-13 07-54-29屏幕截图.png)

![](/home/ychen/图片/2020-11-13 07-55-10屏幕截图.png)

#### 代理模式

#### 单例模式

### UUID的应用

##### 主键: int/bigint

##### 1.以前为什么用整型?

- 因为整型能够自动递增,开发方便

##### 2.实际项目很少使用整型作为主键,就是因为自动递增的问题

##### 3.实际项目使用字符串作为主键比较多

##### 	主键:

- 非空 + 唯一

  - 随机数: 416836

  - 时间: 202011122053 + 416836

  - UUID
    - 生成一组由数字字母以及横杠所组成的含有36位随机串,这个随机串是唯一的.

##### (1)为什么UUID是唯一的?

- 随机数
- 时间
- **硬件自身出厂机器编码**

##### (2)UUID生成的主键是使用什么类型?

- **varchar(32):变长**
  - "aaaa"
- **char(32):定长**
  - "aaa        "

> char比varchar快



