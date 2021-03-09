# 设计模式

韩顺平
https://www.bilibili.com/video/BV1G4411c7N4?from=search&seid=4889535572362876599

链接:https://pan.baidu.com/s/12QKlBku3SIPq8XeqScNWIQ 密码:ghno

当前(总：149，当前：39) ,选（单例模式，代理模式）

https://www.bilibili.com/video/BV1G4411c7N4?p=39

马士兵
https://www.bilibili.com/video/BV1tK411W7xx?from=search&seid=4889535572362876599

## 一．七大设计原则

实例：com.atguigu.principle

### 1.单一职责原则

​			一个类只负责一项职责。只有当逻辑足够简单，才可以在代码级别违反单一原则：只有类中方法数量足够少，可以在方法级别保持单一原则

实例：com.atguigu.principle.singleresponsibility

### 2.接口隔离原则

​			客户端不应该依赖它不需要的接口，根据需求独立出不同的接口，即一个类对另一个类的依赖应该建立在最小接口上。

实例：com.atguigu.principle.segregation

根据要求画出uml流程图？？？

### 3.依赖倒转原则

​		高层模块不应该依赖底层模块，二者都应该依赖抽象

​		抽象不应该依赖细节，细节应该依赖抽象

​		依赖传递方式：接口传递，构造方法传递，setter方式传递（实例：DependencyPass.java）

实例：com.atguigu.principle.inversion

### 4.里式替换原则

​		继承编程思考.

​		父类实现的方法，实际上是设定的规范与契约．

​		子类继承父类，尽量不要重写父类方法．在适当情况下可以使用聚合，组合依赖关系来解决问题

实例：com.atguigu.principle.liskov

### 5.开闭原则

　　　一个软件实体如类，模块，函数 ：对外扩展开放（提供方），对修改关闭（使用方）。当有一个软件需求时，应该是扩展软件的具体行为，而不是通过修改已有的代码实现

实例：com.atguigu.principle.ocp

### 6.迪米特原则

　　　一个对象应该保持其他对象最少了解，否则（类与类耦合性较大）

　　　直接的朋友：每个对象与其他对象有耦合关系，方式（成员变量，方法参数，方法返回值），依赖，关联，组合，聚合。

出现在局部变量的类不是思考直接的朋友，也就是说陌生的类最好不要以局部变量的形式出现在类的内部

实例：com.atguigu.principle.demeter

### 7.合成复用原则

　　　尽量使用合成/聚合的方式，而不是使用继承

实例：com.atguigu.principle.composite

## 二．UML设计

实例：com.atguigu.uml

### 1.UML图类型

> 静态结构图：
>
> 。类图(基础)	。对象图	。包图	。组件图	。部署图
>
> 动态行为图：
>
> 。交互图（时序协图与协作图）	。状态图	。活动图

### 2.类图

Relation:

​	1.Dependency	表示依赖（类中使用到对方，类的成员变量，方法的参数或返回类型，方法中使用到了）

​	2.Association	表示关联（类与类之间关系，分为　单向依赖　和　双向依赖,还有多对多单向与双向关系）

​	3.Generalization表示泛化（Ａ继承Ｂ，就存在Ａ和Ｂ存在泛化关系．．．）

​	4.Realization	表示实现（Ａ类实现一个接口方法，Ａ实现了接口关系）

​	5.Aggregation	表示聚合（A有个成员变量，里面通过set方法设置，整体和部分可以分开的关系）

​	6.Composite	表示组合(聚合升级组合)（B类里面有A a=new A(),耦合较高,整体和部分不能分开的关系）

## 三．设计模式类型

### .[创建型模式](create.md)

<font color="blue">１．单例模式</font>　<font color="blue">２．抽象工厂模式</font>　<font color="blue">３．原型模式</font>　<font color="blue">４．建造者模式</font>　<font color="blue">５．工厂模式</font>

### .[结构型模式](structure.md)

<font color="blue">１．适配器模式</font>　<font color="blue">２．桥接模式</font>　<font color="blue">３．装饰模式</font>　<font color="blue">４．组合模式</font>　<font color="blue">５．外观模式</font>

<font color="blue">６．享元模式</font>　<font color="blue">７．代理模式</font>

### .行为型模式

<font color="blue">１．模板方法模式</font>　<font color="blue">２．命令模式</font>　<font color="blue">３．访问者模式</font>　<font color="blue">４．迭代器模式</font>

５．观察者模式　６．中介者模式　７．备忘录模式　

８．解释器模式（interpreter模式）　９．状态模式　１０．策略模式

１１．职责链模式（责任链模式）