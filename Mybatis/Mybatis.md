## Mybatis

### 常用开发框架

#### 前端开发框架

##### Angular.js

##### React.js

##### Vue.js

#### 前端UI框架

##### Extjs

##### jquery ui

##### easy ui

##### bootstrap(流量小生)

##### layui(国产,坑多且按年收费)

#### 后端框架

#### 表现层框架(Controller): servlet

##### struts

##### xwork(被收购改为struct2)

##### springmvc(火~)

#### 持久层框架(Dao) : JDBC

##### Hibernate   hql(实现简单查询,复杂sql不行)

- 增删改查方便,全自动

##### ibatis(比Hibernate更自由)

- 在xml文件写sql语句,脱离了字符串的束缚
- 可动态sql

##### Mybatis(ibatis改名后)

#### 整合框架

##### EJB

##### spring

##### SSH: struts/struts2 spring hibernate

##### SSM: springmvc spring mybatis

### Mybatis概述

#### 封装了JDBC几乎所以的数据库操作,使开发者只需要关注SQL本身

##### 加载驱动

##### 创建connection

##### 创建statement

##### 手动设置参数

##### 结果集检索等繁琐操作





#### 如果遇到了海量级别的数据,该如何提高查询的效率?

##### 基础操作

- 对于常用的查询条件的字段,设置索引

##### 高级操作

- 使用nosql数据库,如: redis

##### 专业操作(针对于电商行业)

- 搜索引擎
  - Elasticsearch
  - Solr



#### 1.#{}和${}的区别是什么?

- ${}是Properties文件中的变量占位符,可以用于标签属性值和sql内部,属于静态文本替换，比如${driver}会被静态替换为`com.mysql.jdbc.Driver`

```xml
<property name="driver" value="${driver}"/>
```

- `#{}`是 sql 的参数占位符，MyBatis 会将 sql 中的`#{}`替换为?号，在 sql 执行前会使用 PreparedStatement 的参数设置方法，按序给 sql 的?号占位符设置参数值，比如 ps.setInt(0, parameterValue)，`#{item.name}` 的取值方式为使用反射从参数对象中获取 item 对象的 name 属性值，相当于 `param.getItem().getName()`。

#### 2.Xml 映射文件中，除了常见的 select|insert|updae|delete 标签之外，还有哪些标签？

> `<resultMap>`、`<parameterMap>`、`<sql>`、`<include>`、`<selectKey>`，加上动态 sql 的 9 个标签，`trim|where|set|foreach|if|choose|when|otherwise|bind`等，其中为 sql 片段标签，通过`<include>`标签引入 sql 片段，`<selectKey>`为不支持自增的主键生成策略标签。

#### 3、最佳实践中，通常一个 Xml 映射文件，都会写一个 Dao 接口与之对应，请问，这个 Dao 接口的工作原理是什么？Dao 接口里的方法，参数不同时，方法能重载吗？

> Dao 接口，就是人们常说的 `Mapper`接口，接口的全限名，就是映射文件中的 namespace 的值，接口的方法名，就是映射文件中`MappedStatement`的 id 值，接口方法内的参数，就是传递给 sql 的参数。`Mapper`接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为 key 值，可唯一定位一个`MappedStatement`，举例：`com.ckb.dao.StudentDao.getById`，可以唯一找到 namespace 为`com.ckb.dao.StudentDao`下面`id = findById`的`MappedStatement`。在 MyBatis 中，每一个`<select>`、`<insert>`、`<update>`、`<delete>`标签，都会被解析为一个`MappedStatement`对象。

- **Dao 接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略**
- **Dao 接口的工作原理是 JDK 动态代理，MyBatis 运行时会使用 JDK 动态代理为 Dao 接口生成代理 proxy 对象，代理对象 proxy 会拦截接口方法，转而执行`MappedStatement`所代表的 sql，然后将 sql 执行结果返回。**

#### 4.MyBatis 是如何将 sql 执行结果封装为目标对象并返回的？都有哪些映射形式？

> 第一种是使用`<resultMap>`标签，逐一定义列名和对象属性名之间的映射关系。第二种是使用 sql 列的别名功能，将列别名书写为对象属性名，比如 T_NAME AS NAME，对象属性名一般是 name，小写，但是列名不区分大小写，MyBatis 会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成 T_NAME AS NaMe，MyBatis 一样可以正常工作。

- 有了列名与属性名的映射关系后，MyBatis 通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

#### 5.MyBatis 的 Xml 映射文件中，不同的 Xml 映射文件，id 是否可以重复？

>不同的 Xml 映射文件，如果配置了 namespace，那么 id 可以重复；如果没有配置 namespace，那么 id 不能重复；毕竟 namespace 不是必须的，只是最佳实践而已。原因就是 namespace+id 是作为 `Map<String, MappedStatement>`的 key 使用的，如果没有 namespace，就剩下 id，那么，id 重复会导致数据互相覆盖。有了 namespace，自然 id 就可以重复，namespace 不同，namespace+id 自然也就不同。

#### 6.为什么说 MyBatis 是半自动 ORM 映射工具？它与全自动的区别在哪里？

> Hibernate 属于全自动 ORM 映射工具，使用 Hibernate 查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而 MyBatis 在查询关联对象或关联集合对象时，需要手动编写 sql 来完成，所以，称之为半自动 ORM 映射工具。