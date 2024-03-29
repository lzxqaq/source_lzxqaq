---
title: "简单工厂模式"
date: 2021-12-28T06:50:56+08:00
author: "Shaka"

tags: ["设计模式"]
slug: "singlefactory"
draft: true

---

转载自<https://blog.csdn.net/lovelion/article/details/17517213>

工厂模式是最常用的一类创建型设计模式，通常我们所说的工厂模式是指工厂方法模式，它也是使用频率最高的工厂模式。本章将要学习的简单工厂模式是工厂方法模式的“小弟”，它不属于GoF 23种设计模式，但在软件开发中应用也较为频繁，通常将它作为学习其他工厂模式的入门。此外，工厂方法模式还有一位“大哥”——抽象工厂模式。这三种工厂模式各具特色，难度也逐个加大，在软件开发中它们都得到了广泛的应用，成为面向对象软件中常用的创建对象的工具。

### 一、图表库的设计

> Sunny软件公司欲基于Java语言开发一套图表库，该图表库可以为应用系统提供各种不同外观的图表，例如柱状图、饼状图、折线图等。Sunny软件公司图表库设计人员希望为应用系统开发人员提供一套灵活易用的图表库，而且可以较为方便地对图表库进行扩展，以便能够在将来增加一些新类型的图表。

Sunny软件公司图表库设计人员提出了一个初始设计方案，将所有图表的实现代码封装在一个Chart类中，其框架代码如下所示：

```
class Chart {
private String type; //图表类型

public Chart(Object[][] data, String type) {
this.type = type;
if (type.equalsIgnoreCase("histogram")) {
    //初始化柱状图
}
else if (type.equalsIgnoreCase("pie")) {
    //初始化饼状图
}
else if (type.equalsIgnoreCase("line")) {
    //初始化折线图
}
}

public void display() {
if (this.type.equalsIgnoreCase("histogram")) {
    //显示柱状图
}
else if (this.type.equalsIgnoreCase("pie")) {
    //显示饼状图
}
else if (this.type.equalsIgnoreCase("line")) {
    //显示折线图
}	
}
}
```

客户端代码通过调用Chart类的构造函数来创建图表对象，根据参数type的不同可以得到不同类型的图表，然后再调用display()方法来显示相应的图表。

不难看出，Chart类是一个“巨大的”类，在该类的设计中存在如下几个问题：

(1) 在Chart类中包含很多“if…else…”代码块，整个类的代码相当冗长，代码越长，阅读难度、维护难度和测试难度也越大；而且大量条件语句的存在还将影响系统的性能，程序在执行过程中需要做大量的条件判断。

(2) Chart类的职责过重，它负责初始化和显示所有的图表对象，将各种图表对象的初始化代码和显示代码集中在一个类中实现，违反了“单一职责原则”，不利于类的重用和维护；而且将大量的对象初始化代码都写在构造函数中将导致构造函数非常庞大，对象在创建时需要进行条件判断，降低了对象创建的效率。

(3) 当需要增加新类型的图表时，必须修改Chart类的源代码，违反了“开闭原则”。

(4) 客户端只能通过new关键字来直接创建Chart对象，Chart类与客户端类耦合度较高，对象的创建和使用无法分离。

(5) 客户端在创建Chart对象之前可能还需要进行大量初始化设置，例如设置柱状图的颜色、高度等，如果在Chart类的构造函数中没有提供一个默认设置，那就只能由客户端来完成初始设置，这些代码在每次创建Chart对象时都会出现，导致代码的重复。

面对一个如此巨大、职责如此重，且与客户端代码耦合度非常高的类，我们应该怎么办？本章将要介绍的简单工厂模式将在一定程度上解决上述问题。

### 二、简单工厂模式概述

简单工厂模式并不属于GoF 23个经典设计模式，但通常将它作为学习其他工厂模式的基础，它的设计思想很简单，其基本流程如下：

首先将需要创建的各种不同对象（例如各种不同的Chart对象）的相关代码封装到不同的类中，这些类称为具体产品类，而将它们公共的代码进行抽象和提取后封装在一个抽象产品类中，每一个具体产品类都是抽象产品类的子类；然后提供一个工厂类用于创建各种产品，在工厂类中提供一个创建产品的工厂方法，该方法可以根据所传入的参数不同创建不同的具体产品对象；客户端只需调用工厂类的工厂方法并传入相应的参数即可得到一个产品对象。

简单工厂模式定义如下：

简单工厂模式(Simple Factory Pattern)：定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。

简单工厂模式的要点在于：当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。简单工厂模式结构比较简单，其核心是工厂类的设计，其结构如图1所示：

![img](https://cdn.jsdelivr.net/gh/lzxqaq/jsdelivr@master/image/2021-12-28/factory1.jpeg)

在简单工厂模式结构图中包含如下几个角色：

- Factory（工厂角色）：工厂角色即工厂类，它是简单工厂模式的核心，负责实现创建所有产品实例的内部逻辑；工厂类可以被外界直接调用，创建所需的产品对象；在工厂类中提供了静态的工厂方法factoryMethod()，它的返回类型为抽象产品类型Product。

- Product（抽象产品角色）：它是工厂类所创建的所有对象的父类，封装了各种产品对象的公有方法，它的引入将提高系统的灵活性，使得在工厂类中只需定义一个通用的工厂方法，因为所有创建的具体产品对象都是其子类对象。

- ConcreteProduct（具体产品角色）：它是简单工厂模式的创建目标，所有被创建的对象都充当这个角色的某个具体类的实例。每一个具体产品角色都继承了抽象产品角色，需要实现在抽象产品中声明的抽象方法。


在简单工厂模式中，客户端通过工厂类来创建一个产品类的实例，而无须直接使用new关键字来创建对象，它是工厂模式家族中最简单的一员。

在使用简单工厂模式时，首先需要对产品类进行重构，不能设计一个包罗万象的产品类，而需根据实际情况设计一个产品层次结构，将所有产品类公共的代码移至抽象产品类，并在抽象产品类中声明一些抽象方法，以供不同的具体产品类来实现，典型的抽象产品类代码如下所示：

```
abstract class Product {
//所有产品类的公共业务方法
public void methodSame() {
//公共方法的实现
}

//声明抽象业务方法
public abstract void methodDiff();
}

```

在具体产品类中实现了抽象产品类中声明的抽象业务方法，不同的具体产品类可以提供不同的实现，典型的具体产品类代码如下所示：

```
class ConcreteProduct extends Product {
//实现业务方法
public void methodDiff() {
//业务方法的实现
}
}
```

简单工厂模式的核心是工厂类，在没有工厂类之前，客户端一般会使用new关键字来直接创建产品对象，而在引入工厂类之后，客户端可以通过工厂类来创建产品，在简单工厂模式中，工厂类提供了一个静态工厂方法供客户端使用，根据所传入的参数不同可以创建不同的产品对象，典型的工厂类代码如下所示：

```
class Factory {
//静态工厂方法
public static Product getProduct(String arg) {
Product product = null;
if (arg.equalsIgnoreCase("A")) {
    product = new ConcreteProductA();
    //初始化设置product
}
else if (arg.equalsIgnoreCase("B")) {
    product = new ConcreteProductB();
    //初始化设置product
}
return product;
}
}
```

在客户端代码中，我们通过调用工厂类的工厂方法即可得到产品对象，典型代码如下所示：

```
class Client {
public static void main(String args[]) {
Product product; 
product = Factory.getProduct("A"); //通过工厂类创建产品对象
product.methodSame();
product.methodDiff();
}
}
```

### 三、完整解决方案

为了将Chart类的职责分离，同时将Chart对象的创建和使用分离，Sunny软件公司开发人员决定使用简单工厂模式对图表库进行重构，重构后的结构如图2所示：

![img](https://cdn.jsdelivr.net/gh/lzxqaq/jsdelivr@master/image/2021-12-28/factory2.jpeg)

在上图中，Chart接口充当抽象产品类，其子类HistogramChart、PieChart和LineChart充当具体产品类，ChartFactory充当工厂类。完整代码如下所示：

```
//抽象图表接口：抽象产品类
interface Chart {
public void display();
}

//柱状图类：具体产品类
class HistogramChart implements Chart {
public HistogramChart() {
System.out.println("创建柱状图！");
}

public void display() {
System.out.println("显示柱状图！");
}
}

//饼状图类：具体产品类
class PieChart implements Chart {
public PieChart() {
System.out.println("创建饼状图！");
}

public void display() {
System.out.println("显示饼状图！");
}
}

//折线图类：具体产品类
class LineChart implements Chart {
public LineChart() {
System.out.println("创建折线图！");
}

public void display() {
System.out.println("显示折线图！");
}
}

//图表工厂类：工厂类
class ChartFactory {
//静态工厂方法
public static Chart getChart(String type) {
Chart chart = null;
if (type.equalsIgnoreCase("histogram")) {
    chart = new HistogramChart();
    System.out.println("初始化设置柱状图！");
}
else if (type.equalsIgnoreCase("pie")) {
    chart = new PieChart();
    System.out.println("初始化设置饼状图！");
}
else if (type.equalsIgnoreCase("line")) {
    chart = new LineChart();
    System.out.println("初始化设置折线图！");			
}
return chart;
}
}
```

编写如下客户端测试代码：

```
class Client {
public static void main(String args[]) {
Chart chart;
chart = ChartFactory.getChart("histogram"); //通过静态工厂方法创建产品
chart.display();
}
}
```

编译并运行程序，输出结果如下：

```
创建柱状图！

初始化设置柱状图！

显示柱状图！
```

在客户端测试类中，我们使用工厂类的静态工厂方法创建产品对象，如果需要更换产品，只需修改静态工厂方法中的参数即可，例如将柱状图改为饼状图，只需将代码：

```
chart = ChartFactory.getChart("histogram");
```

改为：

```
chart = ChartFactory.getChart("pie");
```

编译并运行程序，输出结果如下：

```
创建饼状图！

初始化设置饼状图！

显示饼状图！
```

### 四、方案的改进

Sunny软件公司开发人员发现在创建具体Chart对象时，每更换一个Chart对象都需要修改客户端代码中静态工厂方法的参数，客户端代码将要重新编译，这对于客户端而言，违反了“开闭原则”，有没有一种方法能够在不修改客户端代码的前提下更换具体产品对象呢？答案是肯定的，下面将介绍一种常用的实现方式。

我们可以将静态工厂方法的参数存储在XML或properties格式的配置文件中，如下config.xml所示：

```
<?xml version="1.0"?>
<config>
<chartType>histogram</chartType>
</config>
```

再通过一个工具类XMLUtil来读取配置文件中的字符串参数，XMLUtil类的代码如下所示：

```
import javax.xml.parsers.*;
import org.w3c.dom.*;
import org.xml.sax.SAXException;
import java.io.*;

public class XMLUtil {
//该方法用于从XML配置文件中提取图表类型，并返回类型名
public static String getChartType() {
try {
    //创建文档对象
    DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();
    DocumentBuilder builder = dFactory.newDocumentBuilder();
    Document doc;							
    doc = builder.parse(new File("config.xml")); 

    //获取包含图表类型的文本节点
    NodeList nl = doc.getElementsByTagName("chartType");
    Node classNode = nl.item(0).getFirstChild();
    String chartType = classNode.getNodeValue().trim();
    return chartType;
}   
catch(Exception e) {
    e.printStackTrace();
    return null;
}
}
}
```

在引入了配置文件和工具类XMLUtil之后，客户端代码修改如下：

```
class Client {
public static void main(String args[]) {
Chart chart;
String type = XMLUtil.getChartType(); //读取配置文件中的参数
chart = ChartFactory.getChart(type); //创建产品对象
chart.display();
}
}
```

不难发现，在上述客户端代码中不包含任何与具体图表对象相关的信息，如果需要更换具体图表对象，只需修改配置文件config.xml，无须修改任何源代码，符合“开闭原则”。

### 五、简单工厂模式的简化

有时候，为了简化简单工厂模式，我们可以将抽象产品类和工厂类合并，将静态工厂方法移至抽象产品类中，如图3所示：

![img](https://cdn.jsdelivr.net/gh/lzxqaq/jsdelivr@master/image/2021-12-28/factory3.jpeg)

在图3中，客户端可以通过产品父类的静态工厂方法，根据参数的不同创建不同类型的产品子类对象，这种做法在JDK等类库和框架中也广泛存在。


### 六、简单工厂模式总结

简单工厂模式提供了专门的工厂类用于创建对象，将对象的创建和对象的使用分离开，它作为一种最简单的工厂模式在软件开发中得到了较为广泛的应用。

1. 主要优点

简单工厂模式的主要优点如下：

(1) 工厂类包含必要的判断逻辑，可以决定在什么时候创建哪一个产品类的实例，客户端可以免除直接创建产品对象的职责，而仅仅“消费”产品，简单工厂模式实现了对象创建和使用的分离。

(2) 客户端无须知道所创建的具体产品类的类名，只需要知道具体产品类所对应的参数即可，对于一些复杂的类名，通过简单工厂模式可以在一定程度减少使用者的记忆量。

(3) 通过引入配置文件，可以在不修改任何客户端代码的情况下更换和增加新的具体产品类，在一定程度上提高了系统的灵活性。


2. 主要缺点

简单工厂模式的主要缺点如下：

(1) 由于工厂类集中了所有产品的创建逻辑，职责过重，一旦不能正常工作，整个系统都要受到影响。

(2) 使用简单工厂模式势必会增加系统中类的个数（引入了新的工厂类），增加了系统的复杂度和理解难度。

(3) 系统扩展困难，一旦添加新产品就不得不修改工厂逻辑，在产品类型较多时，有可能造成工厂逻辑过于复杂，不利于系统的扩展和维护。

(4) 简单工厂模式由于使用了静态工厂方法，造成工厂角色无法形成基于继承的等级结构。



3. 适用场景

在以下情况下可以考虑使用简单工厂模式：

(1) 工厂类负责创建的对象比较少，由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂。

(2) 客户端只知道传入工厂类的参数，对于如何创建对象并不关心。
