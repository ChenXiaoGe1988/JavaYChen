## 客户关系管理系统(CRM)-用户登录处理

### 画出时序图

> 用来描绘从浏览器到服务器执行全流程的结构图

![](/home/ychen/图片/crm开发时序图-登录.png)

### 1.创建用户表及初始化测试数据

- sql文件

```sql
drop table if exists tbl_user;
create table tbl_user
(
   id                   char(32) not null comment 'uuid',
   loginAct             varchar(255),
   name                 varchar(255),
   loginPwd             varchar(255) comment '密码不能采用明文存储，采用密文，MD5加密之后的数据',
   email                varchar(255),
   expireTime           char(19) comment '失效时间为空的时候表示永不失效，失效时间为2018-10-10 10:10:10，则表示在该时间之前该账户可用。',
   lockState            char(1) comment '锁定状态为空时表示启用，为0时表示锁定，为1时表示启用。',
   deptno               char(4),
   allowIps             varchar(255) comment '允许访问的IP为空时表示IP地址永不受限，允许访问的IP可以是一个，也可以是多个，当多个IP地址的时候，采用半角逗号分隔。允许IP是192.168.100.2，表示该用户只能在IP地址为192.168.100.2的机器上使用。',
   createTime           char(19),
   createBy             varchar(255),
   editTime             char(19),
   editBy               varchar(255),
   primary key (id)
);
INSERT INTO `tbl_user` VALUES ('06f5fc056eac41558a964f96daa7f27c', 'ls', '李四', '202cb962ac59075b964b07152d234b70', 'ls@163.com', '2018-11-27 21:50:05', '1', 'A001', '192.168.1.1', '2018-11-22 12:11:40', '李四', null, null);
INSERT INTO `tbl_user` VALUES ('40f6cdea0bd34aceb77492a1656d9fb3', 'zs', '张三', '202cb962ac59075b964b07152d234b70', 'zs@qq.com', '2018-11-30 23:50:55', '1', 'A001', '192.168.1.1,192.168.1.2,127.0.0.1', '2018-11-22 11:37:34', '张三', null, null);
```

- MD5加密方法

```java
package com.ckb.crm.utils;

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
/**
 * @author ychen 
 */
public class MD5Util {
	public static String getMD5(String password) {
		try {
			// 得到一个信息摘要器
			MessageDigest digest = MessageDigest.getInstance("md5");
			byte[] result = digest.digest(password.getBytes());
			StringBuffer buffer = new StringBuffer();
			// 把每一个byte 做一个与运算 0xff;
			for (byte b : result) {
				// 与运算
				int number = b & 0xff;// 加盐
				String str = Integer.toHexString(number);
				if (str.length() == 1) {
					buffer.append("0");
				}
				buffer.append(str);
			}
			// 标准的md5加密后的结果
			return buffer.toString();
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
			return "";
		}
	}
}
```

### 2.用户登录需求

#### (1)页面加载完成后,"用户名"自动获得焦点

```javascript
$("#loginAct").focus();
```

#### (2)用户点击登录或者敲回车键均可登录

```javascript
$("#submitBtn").click(function () {
      login();
})
$(window).keydown(function (event) {
                //alert(event.keyCode);
                //如果取得的键位的码值为13，表示敲的是回车键
                if (event.keyCode == 13) {
                    login();
                }
            })
```

#### (3)登录采用ajax post

```javascript
			//使用ajax局部刷新 异步请求
            $.ajax({
                url: "settings/user/login.do", 
                data: {
                    "loginAct": loginAct,
                    "loginPwd": loginPwd
                },
                type: "post",
                dataType: "json",
                success: function (data) {
                    //如果登录成功
                    if (data.success) {
                        //跳转到工作台的初始页（欢迎页）
                        window.location.href = "workbench/index.jsp";
                        //如果登录失败
                    } else {
                        $("#msg").html(data.msg);
                    }
                }
            })
```

#### (4)登录时用户名和密码不能为空

```javascript
        /*登录方法*/
        function login() {
            //验证账号密码不能为空
            //取得账号密码
            //将文本中的左右空格去掉，使用$.trim(文本)
            var loginAct = $.trim($("#loginAct").val());
            var loginPwd = $.trim($("#loginPwd").val());
            if (loginAct == "" || loginPwd == "") {
                //针对于标签对 用html(值)
                $("#msg").html("账号密码不能为空");
                //如果账号密码为空，则需要及时强制终止该方法,防止再执行以下的方法
                return false;
            }
```

### 2.使用异常机制来完成异常信息提示

```java
package com.ckb.crm.exception;
/**
 * @author ychen
 */
public class LoginException extends Exception {
    public LoginException(String msg) {
        super(msg);
    }
}
```

### 3.登录条件

#### (1)验证用户名密码正确

- 编写用户控制器 UserController.java

```java

package com.ckb.crm.settings.web.controller;

import com.ckb.crm.settings.domain.User;
import com.ckb.crm.settings.service.UserService;
import com.ckb.crm.settings.service.impl.UserServiceImpl;
import com.ckb.crm.utils.MD5Util;
import com.ckb.crm.utils.PrintJson;
import com.ckb.crm.utils.ServiceFactory;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

/**
 * @author ychen
 */
public class UserController extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("进入到用户控制器");
        String path = request.getServletPath();
        if ("/settings/user/login.do".equals(path)) {
            login(request, response);
        }else if ("/settings/user/xxx.do".equals(path)) {
            //xxx(request,response);
        }
    }

    /**
    * 登录验证
    */
    private void login(HttpServletRequest request, HttpServletResponse response) {
        System.out.println("进入到验证登录操作");
        String loginAct = request.getParameter("loginAct");
        String loginPwd = request.getParameter("loginPwd");
        //进行md5加密
        loginPwd = MD5Util.getMD5(loginPwd);
        //接受浏览器的ip地址
        String ip = request.getRemoteAddr();
        System.out.println("------ip: " + ip);

        //未来业务层开发,统一使用代理类形态的接口对象
        UserService us = (UserService) ServiceFactory.getService(new UserServiceImpl());

        try{
            User user = us.login(loginAct, loginPwd, ip);
            request.getSession().setAttribute("user", user);
            //如果执行到此处,说明业务层没有抛出任何异常
            //表示登录成功
            //{"success":true}
            PrintJson.printJsonFlag(response,true);
        } catch (Exception e) {
            e.printStackTrace();
            String msg = e.getMessage();
            //(1)打包成map,将map解析为json串
            //(2)创建一个Vo(大量使用信息的情况下)
            Map<String, Object> map = new HashMap<String, Object>();
            map.put("success", false);
            map.put("msg", msg);
            PrintJson.printJsonObj(response, map);
        }
    }
}

```

- web.xml注册

```xml
<servlet>
  <servlet-name>UserController</servlet-name>
  <servlet-class>com.ckb.crm.settings.web.controller.UserController</servlet-class>
</servlet>
  <servlet-mapping>
    <servlet-name>UserController</servlet-name>
    <url-pattern>/settings/user/login.do</url-pattern>
  </servlet-mapping>
```

- UserService.java

```java
public interface UserService {
    User login(String loginAct, String loginPwd, String ip) throws LoginException;
}
```

- UserServiceImpl.java

```java
package com.ckb.crm.settings.service.impl;

import com.ckb.crm.exception.LoginException;
import com.ckb.crm.settings.dao.UserDao;
import com.ckb.crm.settings.domain.User;
import com.ckb.crm.settings.service.UserService;
import com.ckb.crm.utils.DateTimeUtil;
import com.ckb.crm.utils.SqlSessionUtil;
import java.util.HashMap;
import java.util.Map;

/**
 * @author ychen
 */
public class UserServiceImpl implements UserService {
    private UserDao userDao = SqlSessionUtil.getSqlSession().getMapper(UserDao.class);

    /**
     * 用户登录方法
     * @param loginAct
     * @param loginPwd
     * @return
     */
    @Override
    public User login(String loginAct, String loginPwd, String ip) throws LoginException {
        Map<String, String> map = new HashMap<String, String>();
        map.put("loginAct", loginAct);
        map.put("loginPwd", loginPwd);
        User user = userDao.login(map);
        //1.账号密码验证
        if (user == null) {
            throw new LoginException("账号密码错误");
        }
        //...
        return user;
    }
}
```

- UserDao接口

```java
public interface UserDao {
    public User login(Map<String, String> map);
}
```

- UserDao.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--接口的全限定名-->
<mapper namespace="com.ckb.crm.settings.dao.UserDao">
	<select id="login" resultType="User">
        select * from tbl_user where loginAct=#{loginAct} and loginPwd=#{loginPwd};
    </select>
</mapper>
```

#### (2)验证失效时间

```java
		//验证失效时间
		String expireTime = user.getExpireTime();
        String currentTime = DateTimeUtil.getSysTime();
        if (expireTime.compareTo(currentTime) < 0) {
            throw new LoginException("账号已失效");
        }
```

#### (3)验证锁定状态

```java
		//验证锁定状态
        String lockState = user.getLockState();
        if ("0".equals(lockState)) {
            throw new LoginException("账号已锁定");
        }
```

#### (4)验证ip地址

```java
		//判断ip地址
        String allowIps = user.getAllowIps();
        if(!allowIps.contains(ip)){
            throw new LoginException("ip地址受限");
        }
```

#### 登录成功后跳转到workbench/index.jsp

- 登录界面(login.jsp)

![](/home/ychen/图片/2020-11-17 10-08-20屏幕截图.png)

- 登录跳转到后台workbench(在工作台初始页展现用户名"张三")

![](/home/ychen/图片/2020-11-17 10-08-44屏幕截图.png)

### 4.登录验证的过滤器

> 所有用户必须登录之后才能访问web资源

- 拦截 *.do *.jsp

```xml
<filter>
    <filter-name>LoginFilter</filter-name>
    <filter-class>com.ckb.crm.web.filter.LoginFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>LoginFilter</filter-name>
    <url-pattern>*.do</url-pattern>
    <url-pattern>*.jsp</url-pattern>
  </filter-mapping>
```

- 注册处理字符的过滤器

```xml
<filter>
  <filter-name>EncodingFilter</filter-name>
  <filter-class>com.ckb.crm.web.filter.EncodingFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>EncodingFilter</filter-name>
  <url-pattern>*.do</url-pattern>
</filter-mapping>
```

- 过滤器EncodingFilter

```java
package com.ckb.crm.web.filter;

import javax.servlet.*;
import java.io.IOException;

/**
 * @author ychen
 */
public class EncodingFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {}

    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws IOException, ServletException {
        System.out.println("进入到过滤字符编码的过滤器");
        //过滤post请求中文参数乱码
        req.setCharacterEncoding("UTF-8");
        //过滤响应流响应中文乱码
        resp.setContentType("text/html;charset=utf-8");
        //将请求放行
        chain.doFilter(req, resp);
    }

    @Override
    public void destroy() {}
}
```



- 过滤器LoginFilter

```java
package com.ckb.crm.web.filter;

import com.ckb.crm.settings.domain.User;
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

/**
 * @author ychen
 */
public class LoginFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {}

    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws IOException, ServletException {
        System.out.println("进入到验证有没有登录过的浏览器");
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) resp;
        String path = request.getServletPath();
        //不应该拦截的资源自动放行
        if ("/login.jsp".equals(path) || "/settings/user/login.do".equals(path)) {
            chain.doFilter(req,resp);
        } else {
            HttpSession session = request.getSession();
            User user = (User) session.getAttribute("user");
            //如果user不为null,说明登录过
            if (user!=null){
                //将请求放行
                chain.doFilter(req, resp);
            }else { //没有登录过
                //重定向到登录页
                response.sendRedirect(request.getContextPath() + "/login.jsp");
            }
        }
    }

    @Override
    public void destroy() {}
}
```

> z12825Z