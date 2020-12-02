## Java基础知识

### Java的特点

- 功能强大和简单易用
- 面向对象(封装,继承,多态)

> 面向对象是一种软件开发思想。它是对现实世界的一种抽象，面向对象会把相关的数据和方法组织为一个整体来看待。

- Java 摒弃了 C++ 中难以理解的多继承、指针、内存管理等概念；不用手动管理对象的生命周期;
- 平台独立性和可移植性(Java 虚拟机的实现)

>Write once, run anywhere

- 支持多线程

> C++ 语言没有内置的多线程机制，因此必须调用操作系统的多线程功能来进行多线程程序设计，而 Java 语言却提供了多线程支持

- Java 具有高性能
- Java 语言具有健壮性

> Java 的强类型机制、异常处理、垃圾的自动收集等是 Java 程序健壮性的重要保证

- Java 很容易开发分布式项目

> Java 语言支持 Internet 应用的开发，Java 中有 net api，它提供了用于网络应用编程的类库，包括URL、URLConnection、Socket、ServerSocket等。Java的 `RMI（远程方法激活）`机制也是开发分布式应用的重要手段。

### JVM ,JDK 和 JRE

#### JVM

> Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。

#### JDK

> `JDK（Java Development Kit）`称为 Java 开发包或 Java 开发工具，是一个编写 Java 的 Applet 小程序和应用程序的程序开发环境。JDK是整个Java的核心，包括了`Java运行环境（Java Runtime Environment）`，一些`Java 工具` 和 `Java 的核心类库（Java API）`。

#### JRE

> JRE 是 Java 运行时环境。它是运行已编译 Java 程序所需的所有内容的集合，包括 Java 虚拟机（JVM），Java 类库，java 命令和其他的一些基础构件。但是，它不能用于创建新程序。

### Java 和 C++的区别?

- 都是面向对象的语言，都支持封装、继承和多态
- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存
- 在 C 语言中，字符串或字符数组最后都会有一个额外的字符`'\0'`来表示结束。但是，Java 语言中没有结束符这一概念

### continue、break、和return的区别是什么？

- continue ：指跳出当前的这一次循环，继续下一次循环。
- break ：指跳出整个循环体，继续执行循环下面的语句。
- `return;` ：直接使用 return 结束方法执行，用于没有返回值函数的方法
- `return value;` ：return 一个特定值，用于有返回值函数的方法

### ==和equals的区别

- **`==`** : 它的作用是判断两个对象的地址是不是相等。即判断两个对象是不是同一个对象。(**基本数据类型==比较的是值，引用数据类型==比较的是内存地址**)

- **`equals()`** : 它的作用也是判断两个对象是否相等，它不能用于比较基本数据类型的变量。`equals()`方法存在于`Object`类中，而`Object`类是所有类的直接或间接父类。

- `equals()` 方法存在两种使用情况：

  >情况 1：类没有覆盖 `equals()`方法。则通过` equals()`比较该类的两个对象时，等价于通过“==”比较这两个对象。使用的默认是 `Object`类`equals()`方法。
  >
  >情况 2：类覆盖了 `equals()`方法。一般，我们都覆盖 `equals()`方法来两个对象的内容相等；若它们的内容相等，则返回 true(即，认为这两个对象相等)。

### hashCode()介绍:

> `hashCode()` 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。`hashCode() `定义在 JDK 的 `Object` 类中，这就意味着 Java 中的任何类都包含有 `hashCode()` 函数。另外需要注意的是： `Object` 的 hashcode 方法是本地方法，也就是用 c 语言或 c++ 实现的，该方法通常用来将对象的 内存地址 转换为整数之后返回。

**为什么要有 hashCode？**

> 当你把对象加入 `HashSet` 时，`HashSet` 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的 hashcode，`HashSet` 会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 `equals()` 方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，`HashSet` 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。（摘自我的 Java 启蒙书《Head First Java》第二版）。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

### **为什么重写 `equals` 时必须重写 `hashCode` 方法？**

##### 如果两个对象相等，则 hashcode 一定也是相同的。两个对象相等,对两个对象分别调用 equals 方法都返回 true。但是，两个对象有相同的 hashcode 值，它们也不一定是相等的 。**因此，equals 方法被覆盖过，则 `hashCode` 方法也必须被覆盖。**

> `hashCode()`的默认行为是对堆上的对象产生独特值。如果没有重写 `hashCode()`，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

### **为什么两个对象有相同的 hashcode 值，它们也不一定是相等的？**

>因为 `hashCode()` 所使用的杂凑算法也许刚好会让多个对象传回相同的杂凑值。越糟糕的杂凑算法越容易碰撞，但这也与数据值域分布的特性有关（所谓碰撞也就是指的是不同的对象得到相同的 `hashCode`。

> 我们刚刚也提到了 `HashSet`,如果 `HashSet` 在对比的时候，同样的 hashcode 有多个对象，它会使用 `equals()` 来判断是否真的相同。也就是说 `hashcode` 只是用来缩小查找成本。

### Java中的几种基本数据类型是什么？对应的包装类型是什么？各自占用多少字节呢？

- 6种数字类型 ：byte、short、int、long、float、double
- 1种字符类型：char
- 1种布尔型：boolean。

### 8种基本类型的包装类和常量池

> **Java 基本类型的包装类的大部分都实现了常量池技术，即 Byte,Short,Integer,Long,Character,Boolean；前面 4 种包装类默认创建了数值[-128，127] 的相应类型的缓存数据，Character创建了数值在[0,127]范围的缓存数据，Boolean 直接返回True Or False。如果超出对应范围仍然会去创建新的对象。**

### 为什么 Java 中只有值传递？

- **按值调用(call by value)表示方法接收的是调用者提供的值，而按引用调用（call by reference)表示方法接收的是调用者提供的变量地址。一个方法可以修改传递引用所对应的变量值，而不能修改传递值调用所对应的变量值**
- **Java 程序设计语言总是采用按值调用。也就是说，方法得到的是所有参数值的一个拷贝，也就是说，方法不能修改传递给它的任何参数变量的内容。**

### 重载和重写的区别

> 重载就是同样的一个方法能够根据输入数据的不同，做出不同的处理
>
> 重写就是当子类继承自父类的相同方法，输入数据一样，但要做出有别于父类的响应时，你就要覆盖父类方法

#### **重载**

- 发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同。

#### **重写**

- 重写发生在运行期，是子类对父类的允许访问的方法的实现过程进行重新编写。
  - 返回值类型、方法名、参数列表必须相同，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。
  - 如果父类方法访问修饰符为 `private/final/static` 则子类就不能重写该方法，但是被 static 修饰的方法能够被再次声明。
  - 构造方法无法被重写

### 深拷贝 vs 浅拷贝

- **浅拷贝**：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。
- **深拷贝**：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。

### 方法的四种类型

- 无参数无返回值的方法
- 有参数无返回值的方法
- 有返回值无参数的方法
- 有返回值有参数的方法
- return 在无返回值方法的特殊使用

### 面向对象和面向过程的区别

- **面向过程** ：**面向过程性能比面向对象高。** 因为类调用时需要实例化，开销比较大，比较消耗资源，所以当性能是最重要的考量因素的时候，比如单片机、嵌入式开发、Linux/Unix 等一般采用面向过程开发。但是，**面向过程没有面向对象易维护、易复用、易扩展。**
- **面向对象** ：**面向对象易维护、易复用、易扩展。** 因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护。但是，**面向对象性能比面向过程低**。

### 在 Java 中定义一个不做事且没有参数的构造方法的作用

> Java 程序在执行子类的构造方法之前，如果没有用 `super()`来调用父类特定的构造方法，则会调用父类中“没有参数的构造方法”。因此，如果父类中只定义了有参数的构造方法，而在子类的构造方法中又没有用 `super()`来调用父类中特定的构造方法，则编译时将发生错误，因为 Java 程序在父类中找不到没有参数的构造方法可供执行。解决办法是在父类里加上一个不做事且没有参数的构造方法。

### 成员变量与局部变量的区别有哪些？

- 从语法形式上看:成员变量是属于类的，而局部变量是在代码块或方法中定义的变量或是方法的参数；成员变量可以被 public,private,static 等修饰符所修饰，而局部变量不能被访问控制修饰符及 static 所修饰；但是，成员变量和局部变量都能被 final 所修饰。
- 从变量在内存中的存储方式来看:如果成员变量是使用`static`修饰的，那么这个成员变量是属于类的，如果没有使用`static`修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存。
- 从变量在内存中的生存时间上看:成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动消失。
- 成员变量如果没有被赋初值:则会自动以类型的默认值而赋值（一种情况例外:被 final 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值。

### 面向对象三大特征

#### 封装

> 封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。

#### 继承

> 继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。

##### **关于继承如下 3 点请记住：**

- 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。
- 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
- 子类可以用自己的方式实现父类的方法。（以后介绍）。

#### 多态

> 多态，顾名思义，表示一个对象具有多种的状态。具体表现为父类的引用指向子类的实例。

##### **多态的特点:**

- 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
- 对象类型不可变，引用类型可变；
- 方法具有多态性，属性不具有多态性；
- 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
- 多态不能调用“只在子类存在但在父类不存在”的方法；
- 如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法。

### String StringBuffer 和 StringBuilder 的区别是什么? String 为什么是不可变的?

- 简单的来说：`String` 类中使用 final 关键字修饰字符数组来保存字符串，`private final char value[]`，所以` String` 对象是不可变的。
- 而 `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串`char[]value` 但是没有用 `final` 关键字修饰，所以这两种对象都是可变的。
- `StringBuilder` 与 `StringBuffer` 的构造方法都是调用父类构造方法也就是`AbstractStringBuilder` 实现的

##### **线程安全性**

>`String` 中的对象是不可变的，也就可以理解为常量，线程安全。`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

##### **性能**

> 每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

### 反射机制

>JAVA 反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为 java 语言的反射机制。

##### 反射机制优缺点

- **优点：** 运行期类型的判断，动态加载类，提高代码灵活度。
- **缺点：** 1,性能瓶颈：反射相当于一系列解释操作，通知 JVM 要做的事情，性能比直接的 java 代码要慢很多。2,安全问题，让我们可以动态操作改变类的属性同时也增加了类的安全隐患。

##### 反射的应用场景

> **反射是框架设计的灵魂。**

- 我们在使用 JDBC 连接数据库时使用 `Class.forName()`通过反射加载数据库的驱动程序；
- Spring 框架的 IOC（动态加载管理 Bean）创建对象以及 AOP（动态代理）功能都和反射有联系；
- 动态配置实例的属性；

### **谈谈HashMap put 的过程**
#### HashMap put 方法的核心就是在 putval 方法，它的插入过程如下
- 首先会判断 HashMap 中是否是新构建的，如果是的话会首先进行 resize
- 然后判断需要插入的元素在 HashMap 中是否已经存在（说明出现了碰撞情况），如果不存在，直接生成新的k-v 节点存放，再判断是否需要扩容。
- 如果要插入的元素已经存在的话，说明发生了冲突，这就会转换成链表或者红黑树来解决冲突，首先判断链表中的 hash，key 是否相等，如果相等的话，就用新值替换旧值，如果节点是属于 TreeNode 类型，会直接在红黑树中进行处理，如果 hash ,key 不相等也不属于 TreeNode 类型，会直接转换为链表处理，进行链表遍历，如果链表的 next 节点是 null，判断是否转换为红黑树，如果不转换的话，在遍历过程中找到 key 完全相等的节点，则用新节点替换老节点

源码如下:
```JAVA
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
  Node<K,V>[] tab; Node<K,V> p; int n, i;
  // 如果table 为null 或者没有为table分配内存，就resize一次
  if ((tab = table) == null || (n = tab.length) == 0)
    n = (tab = resize()).length;
  // 指定hash值节点为空则直接插入，这个(n - 1) & hash才是表中真正的哈希
  if ((p = tab[i = (n - 1) & hash]) == null)
    tab[i] = newNode(hash, key, value, null);
  // 如果不为空
  else {
    Node<K,V> e; K k;
    // 计算表中的这个真正的哈希值与要插入的key.hash相比
    if (p.hash == hash &&
        ((k = p.key) == key || (key != null && key.equals(k))))
      e = p;
    // 若不同的话，并且当前节点已经在 TreeNode 上了
    else if (p instanceof TreeNode)
      // 采用红黑树存储方式
      e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
    // key.hash 不同并且也不再 TreeNode 上，在链表上找到 p.next==null
    else {
      for (int binCount = 0; ; ++binCount) {
        if ((e = p.next) == null) {
          // 在表尾插入
          p.next = newNode(hash, key, value, null);
          // 新增节点后如果节点个数到达阈值，则进入 treeifyBin() 进行再次判断
          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
            treeifyBin(tab, hash);
          break;
        }
        // 如果找到了同hash、key的节点，那么直接退出循环
        if (e.hash == hash &&
            ((k = e.key) == key || (key != null && key.equals(k))))
          break;
        // 更新 p 指向下一节点
        p = e;
      }
    }
    // map中含有旧值，返回旧值
    if (e != null) { // existing mapping for key
      V oldValue = e.value;
      if (!onlyIfAbsent || oldValue == null)
        e.value = value;
      afterNodeAccess(e);
      return oldValue;
    }
  }
  // map调整次数 + 1
  ++modCount;
  // 键值对的数量达到阈值，需要扩容
  if (++size > threshold)
    resize();
  afterNodeInsertion(evict);
  return null;
}

```
### **Integer缓存池**
#### 也就是IntegerCache,源码如下:
```
private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];
        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;
            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }
        private IntegerCache() {}
    }
    /*它的默认值用于缓存 -128 - 127 之间的数字，如果有 -128 - 127 之间的数字的话，使用 new Integer 不用创建对象，会直接从缓存池中取，此操作会减少堆中对象的分配，有利于提高程序的运行效率。
    例如创建一个 Integer a = 100，其实是调用 Integer 的 valueOf */
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
   
 //如果在指定缓存池范围内的话，会直接返回缓存的值而不用创建新的 Integer 对象。
 //缓存的大小可以使用 XX:AutoBoxCacheMax 来指定，在 VM 初始化时，java.lang.Integer.IntegerCache.high 属性会设置和保存在 sun.misc.VM 的私有系统属性中。
 ```
    



