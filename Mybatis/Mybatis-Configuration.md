## MyBatis  Configuration

### Configuration的创建

```java
private XMLConfigBuilder(XPathParser parser, String environment, Properties props) {
        super(new Configuration());
        this.localReflectorFactory = new DefaultReflectorFactory();
        ErrorContext.instance().resource("SQL Mapper Configuration");
        this.configuration.setVariables(props);
        this.parsed = false;
        this.environment = environment;
        this.parser = parser;
    }
```

- new Configuration()

  ```java
  public Configuration() {
          this.safeResultHandlerEnabled = true;
          this.multipleResultSetsEnabled = true;
          this.useColumnLabel = true;
          this.cacheEnabled = true;
          this.useActualParamName = true;
          this.localCacheScope = LocalCacheScope.SESSION;
          this.jdbcTypeForNull = JdbcType.OTHER;
          this.lazyLoadTriggerMethods = new HashSet(Arrays.asList("equals", "clone", "hashCode", "toString"));
          this.defaultExecutorType = ExecutorType.SIMPLE;
          this.autoMappingBehavior = AutoMappingBehavior.PARTIAL;
          this.autoMappingUnknownColumnBehavior = AutoMappingUnknownColumnBehavior.NONE;
          this.variables = new Properties();
          this.reflectorFactory = new DefaultReflectorFactory();
          this.objectFactory = new DefaultObjectFactory();
          this.objectWrapperFactory = new DefaultObjectWrapperFactory();
          this.lazyLoadingEnabled = false;
          this.proxyFactory = new JavassistProxyFactory();
          this.mapperRegistry = new MapperRegistry(this);
          this.interceptorChain = new InterceptorChain();
          this.typeHandlerRegistry = new TypeHandlerRegistry();
      	//TypeAliasRegistry的初始化
          this.typeAliasRegistry = new TypeAliasRegistry();
          this.languageRegistry = new LanguageDriverRegistry();
          this.mappedStatements = new Configuration.StrictMap("Mapped Statements collection");
          this.caches = new Configuration.StrictMap("Caches collection");
          this.resultMaps = new Configuration.StrictMap("Result Maps collection");
          this.parameterMaps = new Configuration.StrictMap("Parameter Maps collection");
          this.keyGenerators = new Configuration.StrictMap("Key Generators collection");
          this.loadedResources = new HashSet();
          this.sqlFragments = new Configuration.StrictMap("XML fragments parsed from previous mappers");
          this.incompleteStatements = new LinkedList();
          this.incompleteCacheRefs = new LinkedList();
          this.incompleteResultMaps = new LinkedList();
          this.incompleteMethods = new LinkedList();
          this.cacheRefMap = new HashMap();
      	//事务
          this.typeAliasRegistry.registerAlias("JDBC", JdbcTransactionFactory.class);
          this.typeAliasRegistry.registerAlias("MANAGED", ManagedTransactionFactory.class);
      	//数据源工厂
          this.typeAliasRegistry.registerAlias("JNDI", JndiDataSourceFactory.class);
          this.typeAliasRegistry.registerAlias("POOLED", PooledDataSourceFactory.class);
          this.typeAliasRegistry.registerAlias("UNPOOLED", UnpooledDataSourceFactory.class);
      	//几种缓存
          this.typeAliasRegistry.registerAlias("PERPETUAL", PerpetualCache.class);
          this.typeAliasRegistry.registerAlias("FIFO", FifoCache.class);
          this.typeAliasRegistry.registerAlias("LRU", LruCache.class);
          this.typeAliasRegistry.registerAlias("SOFT", SoftCache.class);
          this.typeAliasRegistry.registerAlias("WEAK", WeakCache.class);
      	//供应商的 DataBaseId提供者
          this.typeAliasRegistry.registerAlias("DB_VENDOR", VendorDatabaseIdProvider.class);
      	//Mybatis默认的XML驱动是XMLLanguageDriver, 用来解析动态sql
          this.typeAliasRegistry.registerAlias("XML", XMLLanguageDriver.class);
          this.typeAliasRegistry.registerAlias("RAW", RawLanguageDriver.class);
      	//日志类型
          this.typeAliasRegistry.registerAlias("SLF4J", Slf4jImpl.class);
          this.typeAliasRegistry.registerAlias("COMMONS_LOGGING", JakartaCommonsLoggingImpl.class);
          this.typeAliasRegistry.registerAlias("LOG4J", Log4jImpl.class);
          this.typeAliasRegistry.registerAlias("LOG4J2", Log4j2Impl.class);
          this.typeAliasRegistry.registerAlias("JDK_LOGGING", Jdk14LoggingImpl.class);
          this.typeAliasRegistry.registerAlias("STDOUT_LOGGING", StdOutImpl.class);
          this.typeAliasRegistry.registerAlias("NO_LOGGING", NoLoggingImpl.class);
      	//代理工厂
          this.typeAliasRegistry.registerAlias("CGLIB", CglibProxyFactory.class);
          this.typeAliasRegistry.registerAlias("JAVASSIST", JavassistProxyFactory.class);
      
          this.languageRegistry.setDefaultDriverClass(XMLLanguageDriver.class);
          this.languageRegistry.register(RawLanguageDriver.class);
      }
  ```

  - TypeAliasRegistry

  ```java
  public TypeAliasRegistry() {
          this.registerAlias("string", String.class);
      
          this.registerAlias("byte", Byte.class);
          this.registerAlias("long", Long.class);
          this.registerAlias("short", Short.class);
          this.registerAlias("int", Integer.class);
          this.registerAlias("integer", Integer.class);
          this.registerAlias("double", Double.class);
          this.registerAlias("float", Float.class);
          this.registerAlias("boolean", Boolean.class);
      
          this.registerAlias("byte[]", Byte[].class);
          this.registerAlias("long[]", Long[].class);
          this.registerAlias("short[]", Short[].class);
          this.registerAlias("int[]", Integer[].class);
          this.registerAlias("integer[]", Integer[].class);
          this.registerAlias("double[]", Double[].class);
          this.registerAlias("float[]", Float[].class);
          this.registerAlias("boolean[]", Boolean[].class);
      
          this.registerAlias("_byte", Byte.TYPE);
          this.registerAlias("_long", Long.TYPE);
          this.registerAlias("_short", Short.TYPE);
          this.registerAlias("_int", Integer.TYPE);
          this.registerAlias("_integer", Integer.TYPE);
          this.registerAlias("_double", Double.TYPE);
          this.registerAlias("_float", Float.TYPE);
          this.registerAlias("_boolean", Boolean.TYPE);
      
          this.registerAlias("_byte[]", byte[].class);
          this.registerAlias("_long[]", long[].class);
          this.registerAlias("_short[]", short[].class);
          this.registerAlias("_int[]", int[].class);
          this.registerAlias("_integer[]", int[].class);
          this.registerAlias("_double[]", double[].class);
          this.registerAlias("_float[]", float[].class);
          this.registerAlias("_boolean[]", boolean[].class);
      
          this.registerAlias("date", Date.class);
          this.registerAlias("decimal", BigDecimal.class);
          this.registerAlias("bigdecimal", BigDecimal.class);
          this.registerAlias("biginteger", BigInteger.class);
          this.registerAlias("object", Object.class);
      
          this.registerAlias("date[]", Date[].class);
          this.registerAlias("decimal[]", BigDecimal[].class);
          this.registerAlias("bigdecimal[]", BigDecimal[].class);
          this.registerAlias("biginteger[]", BigInteger[].class);
          this.registerAlias("object[]", Object[].class);
      
          this.registerAlias("map", Map.class);
          this.registerAlias("hashmap", HashMap.class);
          this.registerAlias("list", List.class);
          this.registerAlias("arraylist", ArrayList.class);
          this.registerAlias("collection", Collection.class);
          this.registerAlias("iterator", Iterator.class);
      
          this.registerAlias("ResultSet", ResultSet.class);
      }
  ```

  ### Configuration 的标签以及使用

  - mybatis-config.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!--mybatis配置-->
  <configuration>
  
      <!--外部属性配置 加载类路径下的属性文件-->
      <properties resource="jdbc.properties"/>
      <!-- 为当前包下的每一个类设置一个默认别名 -->
      <typeAliases>
          <package name="com.ckb.crm.settings.domain"/>
          <package name="com.ckb.crm.workbench.domain"/>
  
      </typeAliases>
  
      <!--全局环境配置 配置事务管理,数据源,读取配置文件-->
      <environments default="development">
          <environment id="development">
              <!--事务-->
              <transactionManager type="JDBC"/>
              <!--配置数据源-->
              <dataSource type="POOLED">
                  <!--数据库驱动-->
                  <property name="driver" value="${mysql.driver}"/>
                  <property name="url" value="${mysql.url}"/>
                  <property name="username" value="${mysql.username}"/>
                  <property name="password" value="${mysql.password}"/>
              </dataSource>
          </environment>
      </environments>
  
      <!--引入自定义mapper.xml 完成接口的查找,该接口要与对应的XML命名空间相匹配-->
      <mappers>
          <package name="com.ckb.crm.settings.dao"/>
          <package name="com.ckb.crm.workbench.dao"/>
      </mappers>
  </configuration>
  ```

  - 有关<settings>的配置

  ```xml
  <settings>
  	// 全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。
    <setting name="cacheEnabled" value="true"/>
    
    // 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态。
    <setting name="lazyLoadingEnabled" value="true"/>
    
    // 是否允许单一语句返回多结果集（需要驱动支持）。
    <setting name="multipleResultSetsEnabled" value="true"/>
    
    // 使用列标签代替列名。不同的驱动在这方面会有不同的表现，具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。
    <setting name="useColumnLabel" value="true"/>
    
    // 允许 JDBC 支持自动生成主键，需要驱动支持。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能支持但仍可正常工作（比如 Derby）。
    <setting name="useGeneratedKeys" value="false"/>
    
    // 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。
    <setting name="autoMappingBehavior" value="PARTIAL"/>
    
    // 指定发现自动映射目标未知列（或者未知属性类型）的行为。
    // NONE: 不做任何反应
    // WARNING: 输出提醒日志 ('org.apache.ibatis.session.AutoMappingUnknownColumnBehavior' 的日志等级必须设置为 WARN)
    // FAILING: 映射失败 (抛出 SqlSessionException)
    <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
    
    // 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。
    <setting name="defaultExecutorType" value="SIMPLE"/>
    
    // 设置超时时间，它决定驱动等待数据库响应的秒数。
    <setting name="defaultStatementTimeout" value="25"/>
    
    // 为驱动的结果集获取数量（fetchSize）设置一个提示值。此参数只可以在查询设置中被覆盖。
    <setting name="defaultFetchSize" value="100"/>
    
    // 允许在嵌套语句中使用分页（RowBounds）。如果允许使用则设置为 false
    <setting name="safeRowBoundsEnabled" value="false"/>
    
    // 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。
    <setting name="mapUnderscoreToCamelCase" value="false"/>
    
    // MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。 默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据
    <setting name="localCacheScope" value="SESSION"/>
    
    // 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。
    <setting name="jdbcTypeForNull" value="OTHER"/>
    
    // 指定哪个对象的方法触发一次延迟加载。
    <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
  </settings>
  ```

  - settings的使用

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <!--配置全局设置-->
      <settings>
          <!--启用日志，并指定日志实现方式-->
          <setting name="logImpl" value="SLF4J"/>
  
          <!--启用主键生成策略-->
          <setting name="useGeneratedKeys" value="true"/>
  
          <!--配置启用下划线转驼峰的映射-->
          <setting name="mapUnderscoreToCamelCase" value="true"/>
  
          <!--启用二级缓存-->
          <setting name="cacheEnabled" value="true"/>
      </settings>
  </configuration>
  ```

  

  - Mybatis使用**typeAliases**属性设置别名

  ```xml
  <!-- 为每一个实体类设置一个具体别名 -->
  <typeAliases>
    <typeAlias type="com.kaikeba.beans.Dept" alias="Dept"/>
  </typeAliases>
  
  <!-- 为当前包下的每一个类设置一个默认别名 -->
  <typeAliases>
    <package name="com.mybatis.beans"/>
  </typeAliases>
  ```

  - Mybatis的插件开发

  ```java
  public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
      	//defaultExecutorType默认是简单执行器,如果不传executorType的话,默认使用简单执行器
          executorType = executorType == null ? this.defaultExecutorType : executorType;
          executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
          Object executor;
      	//根据执行器类型生成对应的执行器逻辑
          if (ExecutorType.BATCH == executorType) {
              executor = new BatchExecutor(this, transaction);
          } else if (ExecutorType.REUSE == executorType) {
              executor = new ReuseExecutor(this, transaction);
          } else {
              executor = new SimpleExecutor(this, transaction);
          }
  		//如果允许缓存,则执行缓存执行器
          if (this.cacheEnabled) {
              executor = new CachingExecutor((Executor)executor);
          }
  		//插件开发, 动态代理, 责任链模式
          Executor executor = (Executor)this.interceptorChain.pluginAll(executor);
          return executor;
      }
  ```

  -  StatementHandler, ParameterHandler, ResultSetHandler 的调用和 Executor 基本一致

  ```java
   public ParameterHandler newParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql) {
          ParameterHandler parameterHandler = mappedStatement.getLang().createParameterHandler(mappedStatement, parameterObject, boundSql);
          parameterHandler = (ParameterHandler)this.interceptorChain.pluginAll(parameterHandler);
          return parameterHandler;
      }
  
      public ResultSetHandler newResultSetHandler(Executor executor, MappedStatement mappedStatement, RowBounds rowBounds, ParameterHandler parameterHandler, ResultHandler resultHandler, BoundSql boundSql) {
          ResultSetHandler resultSetHandler = new DefaultResultSetHandler(executor, mappedStatement, parameterHandler, resultHandler, boundSql, rowBounds);
          ResultSetHandler resultSetHandler = (ResultSetHandler)this.interceptorChain.pluginAll(resultSetHandler);
          return resultSetHandler;
      }
  
      public StatementHandler newStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
          StatementHandler statementHandler = new RoutingStatementHandler(executor, mappedStatement, parameterObject, rowBounds, resultHandler, boundSql);
          StatementHandler statementHandler = (StatementHandler)this.interceptorChain.pluginAll(statementHandler);
          return statementHandler;
      }
  ```

  - 通过 MyBatis 提供的强大机制，使用插件是非常简单的，只需实现 Interceptor 接口，并指定想要拦截的方法签名即可

  ```java
  // ExamplePlugin.java
  @Intercepts({@Signature(
    type= Executor.class,
    method = "update",
    args = {MappedStatement.class,Object.class})})
  public class ExamplePlugin implements Interceptor {
    private Properties properties = new Properties();
    public Object intercept(Invocation invocation) throws Throwable {
      // implement pre processing if need
      Object returnObject = invocation.proceed();
      // implement post processing if need
      return returnObject;
    }
    public void setProperties(Properties properties) {
      this.properties = properties;
    }
  }
  ```

  - 只需要再把这个插件告诉 MyBatis， 这里有个插件拦截器

  ```xml
  <!-- mybatis-config.xml -->
  <plugins>
    <plugin interceptor="org.mybatis.example.ExamplePlugin">
      <property name="someProperty" value="100"/>
    </plugin>
  </plugins>
  ```

  #### `typeHandlers` 也叫做类型转换器，主要用在参数转换的地方，哪里进行参数转换呢？其实有两点：

  - PreparedStatementHandler 在解析 SQL 参数，进行参数设置的时候，需要把 Java Type 转换为 JDBC 类型
  - ResultSetHandler 返回的结果集，需要把 JDBC 类型转换为 Java Type

  ##### 编写自己的类型转换器

  ```java
  // ExampleTypeHandler.java
  @MappedJdbcTypes(JdbcType.VARCHAR)
  public class ExampleTypeHandler extends BaseTypeHandler<String> {
  
    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
      ps.setString(i, parameter);
    }
  
    @Override
    public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
      return rs.getString(columnName);
    }
  
    @Override
    public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
      return rs.getString(columnIndex);
    }
  
    @Override
    public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
      return cs.getString(columnIndex);
    }
  }
  ```

  ##### 也需要告诉 MyBatis ，这里面有个参数转换器，别忘了转换！

  ```xml
  <!-- mybatis-config.xml -->
  <typeHandlers>
    <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
  </typeHandlers>
  ```

  ### Configuration 标签的解析

  ##### 在 XMLConfigBuilder 中

  ```java
  private void parseConfiguration(XNode root) {
          try {
              this.propertiesElement(root.evalNode("properties"));
              Properties settings = this.settingsAsProperties(root.evalNode("settings"));
              this.loadCustomVfs(settings);
              this.typeAliasesElement(root.evalNode("typeAliases"));
              this.pluginElement(root.evalNode("plugins"));
              this.objectFactoryElement(root.evalNode("objectFactory"));
              this.objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
              this.reflectorFactoryElement(root.evalNode("reflectorFactory"));
              this.settingsElement(settings);
              this.environmentsElement(root.evalNode("environments"));
              this.databaseIdProviderElement(root.evalNode("databaseIdProvider"));
              this.typeHandlerElement(root.evalNode("typeHandlers"));
              this.mapperElement(root.evalNode("mappers"));
          } catch (Exception var3) {
              throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + var3, var3);
          }
      }
  ```

  

### Configuration 子标签的源码分析

#### 1.Properties 解析

- propertiesElement(root.evalNode("properties"))

```java
// 其实一个个 <> 的标签就是 一个个的XNode节点
  private void propertiesElement(XNode context) throws Exception {
    if (context != null) {
      // 首先判断要解析的属性是否有无子节点
      Properties defaults = context.getChildrenAsProperties();

      // 解析<properties resource=""/> 解析完成就变为配置文件的 SimpleName
      String resource = context.getStringAttribute("resource");

      // 解析<properties url=""/>
      String url = context.getStringAttribute("url");

      // 如果都为空，抛出异常
      if (resource != null && url != null) {
        throw new BuilderException("The properties element cannot specify both a URL and a resource based property file reference.  Please specify one or the other.");
      }

      // 如果不为空的话，就把配置文件的内容都放到 Resources 类中
      if (resource != null) {
        defaults.putAll(Resources.getResourceAsProperties(resource));
      } else if (url != null) {
        defaults.putAll(Resources.getUrlAsProperties(url));
      }
      
      // 这块应该是判断有无之前的配置
      Properties vars = configuration.getVariables();
      if (vars != null) {
        defaults.putAll(vars);
      }
      parser.setVariables(defaults);
      // 最后放入 configuration 属性中
      configuration.setVariables(defaults);
    }
  }
```



#### 2.Settings 解析

- 在这里我们以二级缓存的开启为例来做解析

```xml
<!-- 通知 MyBatis 框架开启二级缓存 -->
<settings>
  <setting name="cacheEnabled" value="true"/>
</settings>
```

- 在`settingsAsProperties(root.evalNode("settings"))` 中是如何解析的呢？

```java
// XNode 就是一个个的 标签
rivate Properties settingsAsProperties(XNode context) {
        if (context == null) {
            return new Properties();
        } else {
            // 获取字标签，字标签也就是 <settings> 中的 <setting>
            Properties props = context.getChildrenAsProperties();
            // Check that all settings are known to the configuration class
  			// 用反射确保所有的设置都在 Configuration 类中。
            MetaClass metaConfig = MetaClass.forClass(Configuration.class, this.localReflectorFactory);
            Iterator var4 = props.keySet().iterator();

            Object key;
            do {
                if (!var4.hasNext()) {
                    return props;
                }

                key = var4.next();
            } while(metaConfig.hasSetter(String.valueOf(key)));
			// 如果反射没有确保这个key 在类中，就抛出异常
            throw new BuilderException("The setting " + key + " is not known.  Make sure you spelled it correctly (case sensitive).");
        }
    }
```

- 解析完成后的 `settings` 对象，底层是用 Hashtable 存储了一个个的 entry 对象。

#### 3.TypeAliases 解析

- TypeAliases 用于别名注册，你可以为实体类指定它的别名

```java
private void typeAliasesElement(XNode parent) {
        if (parent != null) {
            // 也是首先判断有无子标签
            Iterator var2 = parent.getChildren().iterator();
            while(var2.hasNext()) {
                XNode child = (XNode)var2.next();
                String alias;
                // 如果有子标签，那么取出字标签的属性名，如果是 package
                if ("package".equals(child.getName())) {
                    // 那么取出 子标签 的name属性
                    alias = child.getStringAttribute("name");
                    this.configuration.getTypeAliasRegistry().registerAliases(alias);
                } else {
                    // typeAliases 下面有两个标签，一个是 package 一个是 TypeAlias
                    alias = child.getStringAttribute("alias");
                    String type = child.getStringAttribute("type");
                    try {
                        Class<?> clazz = Resources.classForName(type);
                        if (alias == null) {
                            this.typeAliasRegistry.registerAlias(clazz);
                        } else {
                            this.typeAliasRegistry.registerAlias(alias, clazz);
                        }
                    } catch (ClassNotFoundException var7) {
                        throw new BuilderException("Error registering typeAlias for '" + alias + "'. Cause: " + var7, var7);
                    }
                }
            }
        }
    }
```

#### 4.Plugins 解析

- MyBatis 中的插件都在这一步进行解析注册

```java
private void pluginElement(XNode parent) throws Exception {
        if (parent != null) {
            Iterator var2 = parent.getChildren().iterator();
            while(var2.hasNext()) {
                XNode child = (XNode)var2.next();
                // 取出 interceptor 的名称
                String interceptor = child.getStringAttribute("interceptor");
                Properties properties = child.getChildrenAsProperties();
                // 生成新实例，设置属性名
                Interceptor interceptorInstance = (Interceptor)this.resolveClass(interceptor).newInstance();
                interceptorInstance.setProperties(properties);
                // 添加到 configuration 中
                this.configuration.addInterceptor(interceptorInstance);
            }
        }
    }
```

#### 其他步骤

> 其实后面的源码分析步骤都差不多，大体上都是判断有无此 XNode 节点，然后判断它的子节点标签，得到标签的属性，放入 Configuration 对象中，这样就完成了 Configuration 对象的初始化，其实你可以看出，MyBatis 中的 Configuration 也是一个大的容器，来为后面的SQL语句解析和初始化提供保障。