# 附录A 培训实习生——面向对象基础
注：这是阅读《大话设计模式》（程杰 著）的笔记

## 对象

### 一切事物皆为对象，所有的东西都是对象

### 对象是一个自包含的实体，可以用一组可识别的特性和行为来标识

## 面向对象编程

### 针对对象来进行编程

## 类

### 具有相同的属性和功能的对象的抽象的集合

#### 方法、构造方法、析构函数

#### 方法重载

##### 提供了创建多个同名的方法的能力，但参数的类型或个数有所不同

##### 提供了函数可扩展的能力——在不改变原方法的基础上，新增功能

#### 属性

##### 属性是一个方法或一对方法，但在调用它的代码看来，它是一个字段，适合于以字段的方式使用方法调用的场景

#### 变量

##### 存储类满足设计所需要的数据

#### 修饰符

##### public

##### private

##### protected

## 实例

### 属于某一个类的真实的对象

## 三大特性

### 封装

#### 每个对象都包含它能进行的操作所需要的所有信息，因此对象不必依赖其他对象来完成自己的操作

#### 好处

##### 良好的封装能够减少耦合

##### 类内部的实现可以自由地修改

##### 类具有清晰的对外接口

###### 属性

###### 方法

#### 用自己的话说，好处是：良好的封装使得类具有清晰的对外接口，和可灵活修改的内部实现，基于这样的类来搭建软件系统，能够减少耦合

### 继承

#### 继承定义了类如何相互关联、共享特性

#### 继承者除了具备被继承者的特性外，还具备自己独有的个性

##### 猫、老虎、狮子是哺乳动物

##### 钢笔、铅笔、原子笔是笔

#### 继承的工作方式

##### 定义父类和子类，或叫做基类和派生类

##### 子类拥有父类非private的属性和功能

##### 子类可以扩展父类没有的属性和功能

##### 子类可以以自己的方式实现父类的方法（方法重写）

#### 构造方法不能被继承，只能被调用

#### 优点

##### 子类公共的部分都放在类父类，使代码得到了共享，避免了重复，修改或扩展继承而来的实现都较为容易

#### 缺点

##### 继承是一种类与类之间强耦合的关系，父类变，子类跟着变，用得不好就破坏了包装

### 多态

#### 不同的对象可以执行相同的动作，但是要通过他们自己的实现代码来执行

#### 特点

##### 子类以父类的身份出现

##### 子类在工作时使用自己的方法

##### 子类以父类的身份出现时，不可以使用自己特有的属性和方法

#### 实现方式

##### 父类奖成员声明为虚拟的，这样子类在继承父类时就必须重写该成员

##### 虚方法是按照其运行时类型而非编译时类型进行动态绑定调用的/调用位于对象继承链最末端的方法实现

#### 可能出现的问题：子类没有实现相应的方法，在运行中调用会出现异常

## 重构

### 提取父类，把重复的逻辑放到父类，降低代码的重复度

### 敏捷开发的思想：通过重构改善既有代码的设计

## 抽象类

### 实例化没有任何意义的父类可以声明为抽象类

### 实现方式

#### 将父类声明为抽象类，并定义抽象方法（abstract）

### 特点

#### 子类继承抽象类时，必须实现父类的抽象方法

#### 抽象类是用来继承的，不能被实例化

#### 如果类中包含抽象方法，那么类就必须被定义为抽象类

#### 一个类只能继承一个抽象类

## 接口

### 对行为的抽象，把隐式公共方法和属性组合起来，以封装特定功能的一个集合

### 适用于希望给特定类型的对象添加相同的方法，但对象之间的关系又不适合使用继承进行关联的场景

### 一个类可以实现多个接口

## 集合

### 数组

#### 优点：在内存中连续存储，因此可以快速而容易地从头到尾遍历元素；是类型安全的

#### 缺点：创建时必须要指定数组的长度，涉及插入、删除的操作比较困难

### .Net Framework中的集合：用于数据存储和检索的专用类，提供对堆栈、队列、链表和哈希表的支持，大多数集合类实现相同的接口

### 优点

#### 按需调整大小、保证数据的连续性

### 缺点

#### ArrayList不是类型安全的

#### 使用过程中需要进行装箱和拆箱

##### 相对于简单的赋值而言，装箱和拆箱过程（类型转换）需要进行大量的计算

##### 对值类型进行装箱时，必须分配并构造一个全新的对象

## 泛型

### 泛型是具有占位符（类型参数）的类、结构、接口和方法，这些占位符是类、结构、接口和方法所存储或使用的一个或多个类型的占位符

### 泛型集合类

#### 可以将类型参数用作它所存储的对象的类型的占位符

#### 类型参数作为其字段的类型和其方法的参数类型出现

### 使用方法

#### 实例化时指定类型参数

#### 编程过程中若传入错误类型的对象，编译时会报错

### 优点

#### 类型安全

#### 因为使用泛型时不需要装箱拆箱，因此当类型参数为值类型，泛型集合的性能优于非泛型集合

## 委托与事件

### 委托（delegate）是对函数的封装，可以当作给方法的特征指定一个名称

#### 委托是一种引用方法的类型，一旦为委托分配了方法，委托将与该方法具有完全相同的行为

### 事件（event）是委托的一种特殊形式，当发生有意义的事情时，事件对象处理通知过程（观察者模式）

#### 在发生其他类多对象关注的事情时，类或对象可以通过事件通知他们

### 使用方法

#### 声明委托、事件

##### 事件的类型是委托（例如事件“miceRun”的类型是委托函数“CatShoutEventHandler”）

#### 实例化委托，delegateA = new CatShoutEventHandler(mouseA.Run()), delegateB = new CatShoutEventHandler(mouseB.Run())

#### 把委托实例注册到事件中，miceRun += delegateA, miceRun += delegateB

#### “有意义的事情发生时”，执行事件“miceRun()”，效果是执行事件中的所有委托函数实例

