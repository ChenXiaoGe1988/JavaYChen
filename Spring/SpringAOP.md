### cglib代理

##### 定义：[CGLIB](https://github.com/cglib/cglib)(*Code Generation Library*)是一个基于[ASM](http://www.baeldung.com/java-asm)的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。CGLIB 通过继承方式实现代理。很多知名的开源框架都使用到了[CGLIB](https://github.com/cglib/cglib)， 例如 Spring 中的 AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理。

#### 编写cglib代理

##### 步骤:

- 引入cglib.jar包(Spring-core包中已经包含了)
- 定义一个类ProxyFactory
- 自定义 `MethodInterceptor` 并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法，和 JDK 动态代理中的 `invoke` 方法类似
- 通过 `Enhancer` 类的 `create()`创建代理类

##### 注意事项:

- **在内存中构建子类(注意代理的类不能为final类)**
- **目标对象的方法如果为final/static, 那么就不会被拦截**

##### 代码:

```java
//实现MethodInterceptor接口（第一大核心）
public class ProxyFactory implements MethodInterceptor {
    //维护目标对象
    private Object target;
    public ProxyFactory(Object target) {
        this.target = target;
    }

    //给目标对象创建代理对象
    public Object getProxyInstance() {
        //1.工具类（第二大核心）
        Enhancer en = new Enhancer();
        //2.设置父类(即被代理类)
        en.setSuperclass(target.getClass());
        //3.设置回调函数
        en.setCallback(this);
        //4.创建子类(即代理对象)
        return en.create();
    }

    /**拦截被代理类中的方法 
    * @param o 被代理的对象（需要增强的对象）
    * @param method 被拦截的方法（需要增强的方法）
    * @param objects 方法入参
    * @param methodProxy 用于调用原始方法
    */
    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println("开始事务");
        //执行目标对象的方法
        Object object = method.invoke(target, objects);
        System.out.println("提交事务");
        return object;
    }
}
```

##### Junit测试：

```java
	UserDao userDao = new UserDao();
    UserDao factory = (UserDao) new ProxyFactory(userDao).getProxyInstance();
    factory.save();
```



### 手动实现AOP编程

##### 接口

```java
public interface IUser {
    void save();
}
```

##### DAO

```java
//把对象添加到容器中,首字母会小写
//@Repository
@Component
public class UserDao implements IUser {
    public void save() {
        System.out.println("DB:保存用户");
    }
}
```

##### 把AOP加到容器中

```java
//把该对象加入到容器中
@Component
public class AOP {
    public void begin() {
        System.out.println("开始事务");
    }
    public void close() {
        System.out.println("关闭事务");
    }
}
```



##### 代理一：工厂静态方法

```java
public class ProxyFactory{
    //维护目标对象
    private static Object target;
    //维护关键点代码的类
    private static AOP aop;
    //给目标对象创建代理对象
    public static Object getProxyInstance(Object target_, AOP aop_) {
        //目标对象和关键点代码的类都是通过外界传递进来
        target = target_;
        aop = aop_;
        return Proxy.newProxyInstance(
                target.getClass().getClassLoader(), //目标类的类加载
                target.getClass().getInterfaces(), //代理需要实现的接口，可指定多个
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        aop.begin();
                        Object object = method.invoke(target, args);
                        aop.close();
                        return object;
                    }
                }
        );
    }
}
```

##### 配置文件（spring/applicationContext.xml）

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
        
        <!--使用工厂静态方法创建代理对象-->
        <bean id="proxy" class="com.ckb.utils.ProxyFactory" factory-method="getProxyInstance">
                <constructor-arg index="0" ref="userDao" />
                <constructor-arg index="1" ref="AOP" />
        </bean>
        <!--开启注解扫描器-->
        <context:component-scan base-package="com.ckb"/>
</beans>
```

##### jUnit测试

```java
@org.junit.Test
    public void testAOP() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring/applicationContext.xml");
        IUser iUser = (IUser) ac.getBean("proxy");
        iUser.save();
    }
```

##### 效果如下：

![](/home/ychen/图片/手动实现AOP效果图.png)

##### 代理二：使用工厂非静态方法

```java
/**
 * 工厂非静态方法
 */
public class ProxyFactory{
    /**
     * @param target_ 目标对象
     * @param aop_ 关键点代码类的对象
     * @return
     */
    public Object getProxyInstance(final Object target_, final AOP aop_) {
        //目标对象和关键点代码的类都是通过外界传递进来
        return Proxy.newProxyInstance(
                target_.getClass().getClassLoader(),
                target_.getClass().getInterfaces(),
                new InvocationHandler() {
                 	@Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        aop_.begin();
                        Object returnValue = method.invoke(target_, args);
                        aop_.close();
                        return returnValue;
                    }
                }
        );
    }
}

```

##### 配置文件（spring/applicationContext.xml）- 先创建工厂，再创建代理类对象

```java
//省略...
<!--使用非静态工厂创建代理对象-->
        <!--1.创建工厂-->
        <bean id="factory" class="com.ckb.utils.ProxyFactory"/>
        <!--2.通过工厂创建代理类对象-->
        <bean id="IUser" class="com.ckb.dao.interfaces.IUser" factory-bean="factory" factory-method="getProxyInstance">
                <constructor-arg index="0" ref="userDao"/>
                <constructor-arg index="1" ref="AOP"/>
        </bean>
        <!--开启注解扫描器-->
        <context:component-scan base-package="com.ckb"/>
</beans>
```



### AOP概述（面向切面编程）

##### 定义：抽取很多功能都有的重复代码，在运行的时候往业务方法上动态植入“切面类代码”

##### 功能：让关注点代码与业务代码分离

#### 使用Spring AOP 开发步骤:

#### 1.引入aop有关jar包

- pom.xml

```java
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>4.3.7.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.5</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjrt</artifactId>
      <version>1.9.5</version>
    </dependency>
```

#### 2.applicationContext.xml中引入aop名称空间

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```

#### 3.注解方式实现AOP编程

##### 在applicationContext.xml中开启注解方式

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	<!-- 开启注解扫描器 -->
    <context:component-scan base-package="com.aa"/>
    <!-- 开启aop注解方式 -->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

/*更多细节请往下看*/

#### 4.XML方式实现AOP编程

##### 切面类（AOP.java）

```java
//把该对象加入到容器中
@Component
@Aspect//指定为切面类
public class AOP {
    //指定切入点表达式 , 拦截哪个类的哪些方法
    @Pointcut("execution(* com.aa.*.*(..))")
    public void pt() {
    }
    @Before("pt()")
    public void begin() {
        System.out.println("开始事务");
    }
    @After("pt()")
    public void close() {
        System.out.println("关闭事务");
    }
}
```

##### DAO层

- UserDao实现了IUser接口（内部使用动态代理）

```java
@Component
public class UserDao implements IUser {
    @Override
    public void save() {
        System.out.println("DB:成功保存用户");
    }
}
```

- OrderDao没有实现接口（使用cglib代理）

```java
@Component
public class OrderDao {
    public void save() {
        System.out.println("我已经点了外卖了!");
    }
}
```

##### XML文件配置（spring/applicationContext.xml）

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans //略...>
    	<!-- 开启注解扫描器 -->
    	<!-- <context:component-scan base-package="com.aa"/> -->
    	<!-- 开启aop注解方式 -->
    	<!-- <aop:aspectj-autoproxy></aop:aspectj-autoproxy> -->
            
		<!--1.对象实例-->
        <bean id="userDao" class="com.aa.UserDao"/>
        <bean id="orderDao" class="com.aa.OrderDao"/>
        <!--2.切面类-->
        <bean id="aop" class="com.aa.AOP"/>
        <!--3.AOP配置-->
        <aop:config>
                <!--3.1定义切面表达式 拦截哪些方法-->
                <aop:pointcut id="pointCut" expression="execution(* com.aa.*.*(..))"/>
                <!--3.2指定切面类是哪个-->
                <aop:aspect ref="aop">
                        <!--3.3指定来拦截的时候执行切面类的哪些方法-->
                        <aop:before method="begin" pointcut-ref="pointCut"/>
                        <aop:after method="close" pointcut-ref="pointCut"/>
                </aop:aspect>
        </aop:config>
</beans>
```

##### Junit测试

```java
@org.junit.Test
    public void testAOP2() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring/applicationContext.xml");
        //这里得到的是代理对象
        IUser iUser = (IUser) ac.getBean("userDao");
        System.out.println(iUser.getClass());
        iUser.save();
    }

    @org.junit.Test
    public void testAOP3() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring/applicationContext.xml");
        //这里得到的是代理对象
        OrderDao orderDao = (OrderDao) ac.getBean("orderDao");
        System.out.println(orderDao.getClass());
        orderDao.save();
    }
```

##### 测试效果:

- 动态代理

![](/home/ychen/图片/testAOP2.png)

- CGLIB代理

![](/home/ychen/图片/testAOP3.png)