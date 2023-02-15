# UML图

## 基本介绍

**统一建模语言**（英语：Unified Modeling Language，缩写**UML**）是非专利的第三代[建模](https://zh.m.wikipedia.org/wiki/建模语言)和[规约语言](https://zh.m.wikipedia.org/wiki/规约语言)。UML是一种开放的方法，用于说明、可视化、构建和编写一个正在开发的、面向对象的、软件密集系统的制品的开放方法。UML展现了一系列最佳工程实践，这些最佳实践在对大规模，复杂系统进行建模方面，特别是在[软件架构](https://zh.m.wikipedia.org/wiki/软件架构)层次已经被验证有效。

[![img](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/UML_logo.svg/220px-UML_logo.svg.png)](https://zh.m.wikipedia.org/wiki/File:UML_logo.svg)

UML的标志

这个语言由[葛来迪·布区](https://zh.m.wikipedia.org/wiki/葛來迪·布區)，[伊瓦尔·雅各布森](https://zh.m.wikipedia.org/wiki/伊瓦爾·雅各布森)与[詹姆士·兰宝](https://zh.m.wikipedia.org/wiki/詹姆士·蘭寶)于1994年至1995年间，在[Rational Software](https://zh.m.wikipedia.org/w/index.php?title=Rational_Software&action=edit&redlink=1)（英语：[Rational Software](https://en.wikipedia.org/wiki/Rational_Software)）公司中开发，于1996年又进一步发展。UML集成了[Booch](https://zh.m.wikipedia.org/w/index.php?title=Booch&action=edit&redlink=1)，[对象建模技术](https://zh.m.wikipedia.org/w/index.php?title=对象建模技术&action=edit&redlink=1)和[面向对象程序设计](https://zh.m.wikipedia.org/wiki/面向对象程序设计)的概念，将这些方法融合为单一的，通用的，并且可以广泛使用的建模语言。UML打算成为可以对并发和分布式系统的标准建模语言。

1997年，UML被[对象管理组织](https://zh.m.wikipedia.org/wiki/对象管理组织)接纳为标准，并在此之后受该组织管理。2005年，UML被[国际标准化组织](https://zh.m.wikipedia.org/wiki/國際標準化組織)接纳为一种标准[[1\]](https://zh.m.wikipedia.org/zh-sg/统一建模语言#cite_note-1)，自此，该标准被定期修订以涵盖UML的最新版本[[2\]](https://zh.m.wikipedia.org/zh-sg/统一建模语言#cite_note-2)。然而，在软件工程中，大多数从业者不使用UML，而是产生非正式的手绘图；不过，这些图例中仍往往包括UML的元素

-- 来自wiki百科

> 1. UML是一种用户软件系统分析和设计的工具，用户帮助软件开发人员进行思考和记录思路的结果，
> 2. uml本身是一套符号的规定，就像数学符号和化学符号一样，这些用户描述软件模型中的各个元素和他们之间的关系，比如类，接口，实现，泛化，依赖，组合，聚合等。

## UML图分类

- 用例图
- 静态结构图：类图、对象图、包图、组件图、部署图
- 动态行为图：交互图（时序图与协作图）、状态图、活动图

说明：

- 类图是描述类和类之间的关系的，是UML图中最核心的

## 符号说明

![image-20220728165736026](\images\image-20220728165736026.png)

## 类之间的依赖、泛化和实现

### 依赖

依赖关系：只要是在类中用到了对方，就存在依赖关系，如果没有对方，则不能通过编译。

- 类的成员属性
- 方法的返回类型
- 方法接受的参数类型
- 方法中使用到的

![image-20220728164102907](\images\image-20220728164102907.png)

### 泛化

泛化是特殊的继承关系

如果A继承了B类，就说A和B之间存在泛化关系

![image-20220728164659746](\images\image-20220728164659746.png)

### 实现

实现关系实际上就是A类实现B类，是依赖关系的特例

![image-20220728170535804](\images\image-20220728170535804.png)

### 关联

关联关系实际上就是类与类之间的联系，是依赖关系的特例

关联具有导航性：即双向关系和多向关系

关系具有多重性：如： ‘1’：表示有且仅有一个，‘0’：表示0或者多个，‘0，1’：表示0个或者1个，‘n，m’：表示n到m个都可以

‘m…*’：表示至少m个

单向一对一关系：

```java
public class Person {
    private class IDCard;
}

public class IDard{ }
```

双向一对一关系

```java
public class Person {
    private IDCard card;
}

public class IDCard {
    private Person person;
}
```

### 聚合

表示的是整体和部分关系，整体与部分分开，是关联关系的特例。具有关联的导航性和多重性

如：组成电脑的的部件：鼠标，键盘，屏幕……，各个配件都可以从电脑上取出来，没有屏幕的电脑仍然是电脑，只是不方便观察而已。

```java
public class Computer {
    
    // 认为鼠标可以和电脑分离
    private Mouse mouse;
    // 认为屏幕可以和电脑分离    
    private Moniter moniter;
    
    public void setMouse(Mouse mouse) {
        this.mouse = mouse;
    }
    
    public void setMoniter(Moniter moniter) {
        this.moniter = moniter;
    }
}
```

![image-20220728172130757](\images\image-20220728172130757.png)

### 组合

如果我们人认为鼠标、屏幕和电脑不能分离，关系升级为组合关系（同生共死的关系，一个人不能没有头，心脏等身体部分，但是可以没有身份证，手机等。人和头是组合关系、人和手机是聚合关系）



![image-20220728172833311](\images\image-20220728172833311.png)