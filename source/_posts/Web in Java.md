---
title: Web in Java
date: 2018-10-11 22:52:39
tags: 世界之内
---



> To Major in CS，需要培养一种价值观，即计算机的一切都是可以被理解的。计算机与其他自然科学相比，有一点天然优势：即使某些天赋异禀的科学家利用他们无双的天才创见堆砌了计算机科学的大楼，但他们都是人类，逃不开人类的思维方式和三观，他们创造的计算机理论或许晦涩，但都不可能逃离人类大脑的理解边界。在计算机中任何原理都是有理由的，我们是在创造理由，而不是像物理、化学、生物，在寻找理由。
>
> 即使有些问题你现阶段没有明白，但这些问题都必定是可以理解的，只是时间不够或缺少某些先修知识，所以先放一下，等有时间再学习它们。这种心态可以防止你滑入无助的陷阱。 有时候你的潜意识会愚弄你，把很难做成的事情当成无法做成的事情。就像《肖申克的救赎》中表现的那样，Red第一次看到Andy的锤子，他认定Andy肯定逃不出来。而实际上Andy用19年的时间把墙给凿开了，虽然其间各种艰辛，但是结局却很美好。

<!--more-->

[TOC]

![JavaWeb入门导引](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/JavaWeb入门导引.jpg)

Reference:

https://www.zhihu.com/question/23335551



## Java反射机制

### 什么是反射机制

动态语言<——>静态语言

在反射机制中，Java程序可以加载一个运行时才得知名称的class，获悉其完整构造（但不包括methods定义），并生成其对象实体、或对其fields设值、或唤起其methods。这种“看透class”的能力（the ability of the program to examine itself）被称为introspection（内省、内观、反省）。Reflection和introspection是常被并提的两个术语。

从图1我们可以看到java内部在创建对象时的流程，而反射机制实际上就是通过创建的stu对象反向获取Student类的信息。

![Java反射机制](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/Java反射机制.png)

### 反射的API与基本使用

Reference：

https://www.cnblogs.com/ysocean/p/6516248.html

#### API

​	getName()：获得类的完整名字。

　　getFields()：获得类的public类型的属性。
　　getDeclaredFields()：获得类的所有属性。包括private 声明的和继承类
　　getMethods()：获得类的public类型的方法。
　　getDeclaredMethods()：获得类的所有方法。包括private 声明的和继承类
　　getMethod(String name, Class[] parameterTypes)：获得类的特定方法，name参数指定方法的名字，parameterTypes 参数指定方法的参数类型。
　　getConstructors()：获得类的public类型的构造方法。
　　getConstructor(Class[] parameterTypes)：获得类的特定构造方法，parameterTypes 参数指定构造方法的参数类型。
　　newInstance()：通过类的不带参数的构造方法创建这个类的一个对象。

-------

#### 示例

下面是一个Person类作为示例：

```java
package com.ys.reflex;
public class Person {
    //私有属性
    private String name = "Tom";
    //公有属性
    public int age = 18;
    //构造方法
    public Person() {    
    }
    //私有方法
    private void say(){
        System.out.println("private say()...");
    }
    //公有方法
    public void work(){
        System.out.println("public work()...");
    }
}
```

- 得到Class类

```java
//1、通过对象调用 getClass() 方法来获取,通常应用在：比如你传过来一个 Object
//  类型的对象，而我不知道你具体是什么类，用这种方法
　　Person p1 = new Person();
　　Class c1 = p1.getClass();
        
//2、直接通过 类名.class 的方式得到,该方法最为安全可靠，程序性能更高
//  这说明任何一个类都有一个隐含的静态成员变量 class
　　Class c2 = Person.class;
        
//3、通过 Class 对象的 forName() 静态方法来获取，用的最多，
//   但可能抛出 ClassNotFoundException 异常
　　Class c3 = Class.forName("com.ys.reflex.Person");
```

- 获取Fields、Methods、Constructor

```java
//获得类完整的名字
String className = c2.getName();
System.out.println(className);//输出com.ys.reflex.Person
        
//获得类的public类型的属性。
Field[] fields = c2.getFields();
for(Field field : fields){
   System.out.println(field.getName());//age
}
        
//获得类的所有属性。包括私有的
Field [] allFields = c2.getDeclaredFields();
for(Field field : allFields){
    System.out.println(field.getName());//name    age
}
        
//获得类的public类型的方法。这里包括 Object 类的一些方法
Method [] methods = c2.getMethods();
for(Method method : methods){
    System.out.println(method.getName());//work waid equls toString hashCode等
}
        
//获得类的所有方法。
Method [] allMethods = c2.getDeclaredMethods();
for(Method method : allMethods){
    System.out.println(method.getName());//work say
}
        
//获得指定的属性
Field f1 = c2.getField("age");
System.out.println(f1);
//获得指定的私有属性
Field f2 = c2.getDeclaredField("name");
//启用和禁用访问安全检查的开关，值为 true，则表示反射的对象在使用时应该取消 java 语言的访问检查；反之不取消
f2.setAccessible(true);
System.out.println(f2);
                
//创建这个类的一个对象
Object p2 =  c2.newInstance();
//将 p2 对象的  f2 属性赋值为 Bob，f2 属性即为 私有属性 name
f2.set(p2,"Bob");
//使用反射机制可以打破封装性，导致了java对象的属性不安全。 
System.out.println(f2.get(p2)); //Bob
        
//获取构造方法
Constructor [] constructors = c2.getConstructors();
for(Constructor constructor : constructors){
    System.out.println(constructor.toString());//public com.ys.reflex.Person()
}
```

### 反射有什么用

Java反射机制主要提供了以下功能：

- 在运行时判断任意一个对象所属的类；

- 在运行时构造任意一个类的对象；

- 在运行时判断任意一个类所具有的成员变量和方法；

- 在运行时调用任意一个对象的方法；

- 生成动态代理。

Reference：

https://www.cnblogs.com/yrstudy/p/6500982.html

如果没有反射：

```java
interface fruit{  
    public abstract void eat();  
}  
    
class Apple implements fruit{  
    public void eat(){  
        System.out.println("Apple");  
    }  
}  
    
class Orange implements fruit{  
    public void eat(){  
        System.out.println("Orange");  
    }  
}  
    
// 构造工厂类  
// 也就是说以后如果我们在添加其他的实例的时候只需要修改工厂类就行了  
class Factory{  
    public static fruit getInstance(String fruitName){  
        fruit f=null;  
        if("Apple".equals(fruitName)){  
            f=new Apple();  
        }  
        if("Orange".equals(fruitName)){  
            f=new Orange();  
        }  
        return f;  
    }  
}  
class hello{  
    public static void main(String[] a){  
        fruit f=Factory.getInstance("Orange");  
        f.eat();  
    }  
    
}  
```

可以看到，如果我们要增加水果种类，比如增加一个香蕉，那么在工厂类中就需要增加一个对香蕉的处理：

```java
if("Banana".equals(fruitName)){  
            f=new Banama();  
        }
```

一个还行，如果增加成百上千个，就会造成大麻烦。

---

当我们使用反射之后：

```java
interface fruit{  
    public abstract void eat();  
}  
   
class Apple implements fruit{  
    public void eat(){  
        System.out.println("Apple");  
    }  
}  
   
class Orange implements fruit{  
    public void eat(){  
        System.out.println("Orange");  
    }  
}  
   
class Factory{  
    public static fruit getInstance(String ClassName){  
        fruit f=null;  
        try{  
            f=(fruit)Class.forName(ClassName).newInstance();  
        }catch (Exception e) {  
            e.printStackTrace();  
        }  
        return f;  
    }  
}  
class hello{  
    public static void main(String[] a){  
        fruit f=Factory.getInstance("Reflect.Apple");  
        if(f!=null){  
            f.eat();  
        }  
    }  
}
```

我们看到，这段代码的精髓在于

```java
      f=(fruit)Class.forName(ClassName).newInstance();
```

用一句代码就完成了新建对象的任务，这体现了在前文中提到的，***构造任意一个类的对象***的功能，这其实就是对**具体实现的<u>抽象</u>**。

因为抽象，所以完美，这是贯彻人类历史的价值观。

## 前端开发

### HTML

### CSS

![hexadecimal-color](http://img.mukewang.com/54c5b4120001f20808000902.jpg)

#### 盒模型

![box-model1](http://img.mukewang.com/539fbb3a0001304305570259.jpg)

![box-model2](http://img.mukewang.com/543b4cae0001b34304300350.jpg)

#### 布局模型

##### Flow

流动（Flow）是默认的网页布局模式。也就是说网页在默认状态下的 HTML 网页元素都是根据流动模型来分布网页内容的。

流动布局模型具有2个比较典型的**特征**：

- 第一点，**块状元素**都会在所处的**包含元素内**自上而下按顺序垂直延伸分布，因为在默认状态下，块状元素的宽度都为**100%**。实际上，块状元素都会以行的形式占据位置。如右侧代码编辑器中三个块状元素标签(div，h1，p)宽度显示为100%。

- 第二点，在流动模型下，**内联元素**都会在所处的包含元素内从左到右水平分布显示。

##### Float

让两个块状元素并排显示

```css
div{
    width:200px;
    height:200px;
    border:2px red solid;
    float:left;
}
<div id="div1"></div>
<div id="div2"></div>
```

![Float-model](http://img.mukewang.com/540e62c60001c56a06760417.jpg)

##### Layer

层布局模型，就像是图像软件PhotoShop中非常流行的图层编辑功能一样，每个图层能够精确定位操作，但在网页设计领域，由于网页大小的活动性，层布局没能受到热捧。但是在网页上局部使用层布局还是有其方便之处的。

层模型有三种形式：

1. **绝对定位**(position: absolute)

2. **相对定位**(position: relative)

3. **固定定位**(position: fixed)

与absolute定位类型类似，但它的相对移动的坐标是视图（**屏幕内的网页窗口**）本身。由于视图本身是固定的，它不会随浏览器窗口的滚动条滚动而变化，除非你在屏幕中移动浏览器窗口的屏幕位置，或改变浏览器窗口的显示大小，因此固定定位的元素会始终位于浏览器窗口内视图的某个位置，不会受文档流动影响

### JavaScript

#### JS是什么

JavaScript是一种属于网络的脚本语言,已经被广泛用于Web应用开发,常用来为网页添加各式各样的动态功能,为用户提供更流畅美观的浏览效果。通常JavaScript脚本是通过嵌入在HTML中来实现自身的功能的。

##### 组成部分

![javascript组成](https://gss2.bdstatic.com/-fo3dSag_xI4khGkpoWK1HF6hhy/baike/s%3D250/sign=103c88deb21c8701d2b6b5e3177f9e6e/730e0cf3d7ca7bcb3409f115bf096b63f624a89d.jpg)

1. **ECMAScript**，描述了该语组成言的语法和基本对象。

2. **文档对象模型（DOM）**，描述处理网页内容的方法和接口。

3. **浏览器对象模型（BOM）**，描述与浏览器进行交互的方法和接口

#### JS能做什么

- 写入 HTML 输出

- 对事件作出反应

- 改变HTML内容
- 改变HTML图像
- 改变HTML样式

#### DOM

通过 JavaScript，您可以重构整个 HTML 文档。您可以添加、移除、改变或重排页面上的项目。要改变页面的某个东西，JavaScript 就需要获得对 HTML 文档中所有元素进行访问的入口。这个入口，连同对 HTML 元素进行添加、移动、改变或移除的方法和属性，都是通过**文档对象模型**来获得的（**Document Object Model, DOM**）。

#### Cookie

在 Internet 中，Cookie 实际上是指小量信息，是由 Web 服务器创建的，将信息存储在用户计算机上的文件。一般网络用户习惯用其复数形式 Cookies，指某些网站为了辨别用户身份、进行 Session 跟踪而存储在用户本地终端上的数据，而这些数据通常会经过加密处理。

### XML

Reference：

http://www.w3school.com.cn/xml/xml_usedfor.asp

#### 什么是XML

- XML 指可扩展标记语言（*EX*tensible *M*arkup *L*anguage）
- XML 是一种*标记语言*，很类似 HTML
- XML 的设计宗旨是*传输数据*，而非显示数据
- XML 标签没有被预定义。您需要*自行定义标签*。
- XML 被设计为具有*自我描述性*。

#### XML有什么用

- **XML 把数据从 HTML 分离**

如果你需要在 HTML 文档中显示动态数据，那么每当数据改变时将花费大量的时间来编辑 HTML。

通过 XML，数据能够存储在独立的 XML 文件中。这样你就可以专注于使用 HTML 进行布局和显示，并确保修改底层数据不再需要对 HTML 进行任何的改变。

通过使用几行 JavaScript，你就可以读取一个外部 XML 文件，然后更新 HTML 中的数据内容。

- **XML 简化数据共享与传输**

在真实的世界中，计算机系统和数据使用不兼容的格式来存储数据。XML 数据以纯文本格式进行存储，因此提供了一种独立于软件和硬件的数据存储方法。这让创建不同应用程序可以共享的数据变得更加容易。

通过 XML，可以在不兼容的系统之间轻松地交换数据。对开发人员来说，其中一项最费时的挑战一直是在因特网上的不兼容系统之间交换数据。由于可以通过各种不兼容的应用程序来读取数据，以 XML 交换数据降低了这种复杂性。



### Vue.js

Reference：

https://cn.vuejs.org/v2/guide/

#### 什么是Vue.js

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。

Vue.js 的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统。数据和 DOM 已经被建立了关联，所有东西都是**响应式的**。

所以，我们不需要在意DOM，只需要在意代码逻辑即可。

#### Vue入门

##### 声明式渲染

Vue.js 的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统。

```html
<div id="app">
  {{ message }}
</div>
```

```javascript
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

> Hello Vue!

数据和 DOM 已经被建立了关联，所有东西都是**响应式的**。

##### 条件与循环

- 条件

```html
<div id="app-3">
    <p v-if="seen">
        现在你看到我了
    </p>
</div>
```

```javascript
var app3 = new Vue({
    el: '#app-3',
    data: {
        seen: true
    }
})
```

> 现在你看到我了

如果继续在控制台输入 `app3.seen = false`，你会发现之前显示的消息消失了。

这里我们遇到了一点新东西。你看到的 `v-if` 特性被称为**指令**。指令带有前缀 `v-`，以表示它们是 Vue 提供的特殊特性。可能你已经猜到了，它们会在渲染的 DOM 上应用特殊的响应式行为。

- 循环

```html
<div id="app-4">
    <ol>
        <li v-for="todo in todas">
            {{ todo.text }}
        </li>
    </ol>
</div>
```

```javascript
var app4 = new vue({
    el: '#app-4',
    data:{
       todos :[
        { text: 'one' },
        { text: 'two' },
        { text: 'three' }
   ]
  }
})
```

> 1.one
>
> 2.two
>
> 3.three

在控制台里，输入 `app4.todos.push({ text: 'four' })`，你会发现列表最后添加了一个`4.four`。

##### 处理用户输入

```html
<div id="app-5">
    <p>
        {{ message }}
    </p>
    <button v-on:click="reverseMessage">
        逆转信息
    </button>
</div>
```

```javascript
var app5 = new Vue({
    el: '#app-5',
    data: {
        message: 'Hellp Vue.js!'
    },
    methods: {
    	reverseMessage: function(){
    		this.message = this.message.split('').reverse().join('')
		}
	}
})
```

![Vue处理用户输入1](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/Vue处理用户输入1.png)

![Vue处理用户输入2](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/Vue处理用户输入2.png)



##### 组件化应用构建

- 组件（Component）是 Vue.js 最强大的功能之一。

- 组件可以扩展 HTML 元素，封装可重用的代码。

组件系统是 Vue 的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。仔细想想，几乎任意类型的应用界面都可以抽象为一个组件树：

![组件系统](https://cn.vuejs.org/images/components.png)













### FreeMarker

#### 什么是FreeMarker

FreeMarker的设计是被用来生成HTML页面，尤其是通过基于实现了MVC模式的Servlet应用程序。（*使用MVC模式的动态网页的构思，使得你可以将前端设计从程序中分离出来，更加贴合java的各司其职的编程思想，使得代码更简洁且便于维护。*）

模板引擎。

替代JSP。

![FreeMarker工作原理](https://image-static.segmentfault.com/387/404/3874047029-59f5dae810f72_articlex)



## Mysql

### 是什么

MySQL 是最流行的关系型数据库管理系统，在WEB应用方面 MySQL 是最好的RDBMS(Relational Database Management System：关系数据库管理系统)应用软件之一。

### 基本操作

**Reference:**

https://selectstarsql.com

是一个非常棒的sql教程

## MyBatis

![image-20181011235408816](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/MyBatis.png)

### 是什么

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Ordinary Java Object,普通的 Java对象)映射成数据库中的记录。

![Mybatis功能架构](https://gss0.bdstatic.com/-4o3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=4df3ab1439dbb6fd3156ed74684dc07d/0b46f21fbe096b63ea0d41bf0c338744eaf8accc.jpg)

我们把Mybatis的功能架构分为三层：

(1)API接口层：提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理。

(2)数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。它主要的目的是根据调用的请求完成一次数据库操作。

(3)基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理，这些都是共用的东西，将他们抽取出来作为最基础的组件。为上层的数据处理层提供最基础的支撑。

#### 什么是JDBC

JDBC（Java DataBase Connectivity,java数据库连接）是一种用于执行SQL语句的Java API，可以为多种关系数据库提供统一访问，它由一组用Java语言编写的类和接口组成。JDBC提供了一种基准，据此可以构建更高级的工具和接口，使数据库开发人员能够编写数据库应用程序。对Java程序员而言是API，对实现与数据库连接的服务提供商而言是接口模型。





## Spring

![image-20181011235521175](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/Spring.png)

Spring是企业级的Java应用程序开发**[框架](https://baike.baidu.com/item/框架/1212667?fr=aladdin)**。Spring 框架的核心特性是可以用于开发任何 Java 应用程序，但是在 Java EE 平台上构建 web 应用程序是需要扩展的。 Spring 框架的目标是使 J2EE 开发变得更容易使用，通过启用基于 POJO 编程模型来促进良好的编程实践。

为 了 降 低 Java 开 发 的 复 杂 性， Spring 采 取 了 以 下 4 种 关 键 策 略： 

- 基 于 POJO 的 轻 量 级 和 最 小 侵 入 性 编 程； 

- 通 过 依 赖 注 入 和 面 向 接 口 实 现 松 耦 合；

- 基 于 切 面 和 惯 例 进 行 声 明 式 编 程； 

- 通 过 切 面 和 模 板 减 少 样 板 式 代 码。

### 什么是框架(Framework)

框架（Framework）是整个或部分系统的可重用设计，表现为一组抽象构件及构件实例间交互的方法; 另一种定义认为，框架是可被应用开发者定制的应用骨架。前者是从应用方面而后者是从目的方面给出的定义。

可以说，一个框架是一个可复用的设计构件，它规定了应用的体系结构，阐明了整个设计、协作构件之间的依赖关系、责任分配和控制流程，表现为一组抽象类以及其实例之间协作的方法，它为构件复用提供了上下文(Context)关系。因此构件库的大规模重用也需要框架。

软件系统随着业务的发展，变得越来越复杂，不同领域的业务所涉及到的知识、内容、问题非常非常多。如果每次都从头开发，那都是一个很漫长的事情，且并不一定能将它做好。团队协作开发时，没有了统一标准，大家各写各的，同样的重复的功能到处都是。由于没有统一调用规范，很难看懂别人写的代码，出现Bug或二次开发维护时，根本无从下手。（无框架不堪回首的黑暗日子请看前面章节的讲述）

而一个成熟的框架，它是模板化的代码，它会帮我们实现很多基础性的功能，我们只需要专心的实现所需要的业务逻辑就可以了。而很多底层功能操作，就可以完完全全不用做太多的考虑，框架已帮我们实现了。这样的话，整个团队的开发效率可想而知。另外对于团队成员的变动，也不用太过担心，框架的代码规范让我们能轻松的看懂其他开发人员所写的代码。

### 什么是控制反转(Inversion of Control, IOC)

**Reference：**

http://stamen.iteye.com/blog/1489223/

[百度百科-控制反转](https://baike.baidu.com/item/控制反转)

大多数应用程序都是由两个或是更多的类通过彼此的合作来实现企业逻辑，这使得每个对象都需要获取与其合作的对象（也就是它所依赖的对象）的引用。如果这个获取过程要靠自身实现，那么这将导致代码高度耦合并且难以维护和调试。

Class A中用到了Class B的对象b，一般情况下，需要在A的代码中显式的new一个B的对象。

采用依赖注入技术之后，A的代码只需要定义一个私有的B对象，不需要直接new来获得这个对象，而是通过相关的容器控制程序来将B对象在外部new出来并注入到A类里的引用中。而具体获取的方法、对象被获取时的状态由配置文件（如XML）来指定。

可以把IoC模式看作工厂模式的升华，把IoC容器看作是一个大工厂，只不过这个大工厂里要生成的对象都是在XML文件中给出定义的。利用Java 的“反射”编程，根据XML中给出的类定义生成相应的对象。从实现来看，以前在工厂模式里写死了的对象，IoC模式改为配置XML文件，这就把工厂和要生成的对象两者隔离，极大提高了灵活性和可维护性。



贺岁大片在中国已经形成了一个传统，每到年底总有多部贺岁大片纷至沓来让人应接不暇。在所有贺岁大片中，张之亮的《墨攻》算是比较出彩的一部。该片讲述了战国时期墨家人革离帮助梁国反抗赵国侵略的个人英雄主义故事，恢宏壮阔、浑雄凝重的历史场面相当震撼。其中有一个场景：当刘德华所饰演的墨者革离到达梁国都城下，城上梁国守军问到：“来者何人？”刘德华回答：“墨者革离！”我们不妨通过一个Java类为这个“城门叩问”的场景进行编剧，并借此理解IoC的概念： 

```java
public class MoAttack {
   public void cityGateAsk(){
        //①演员直接侵入剧本
	   LiuDeHua ldh = new LiuDeHua();
	   ldh.responseAsk("墨者革离！");
   }
}
```

我们会发现以上剧本在①处，作为具体角色饰演者的刘德华直接侵入到剧本中，使剧本和演员直接耦合在一起（图3-1）。 
![img](http://dl.iteye.com/upload/attachment/0066/7636/f7676f68-e41e-3883-a37f-046ae379c321.jpg)
一个明智的编剧在剧情创作时应围绕故事的角色进行，而不应考虑角色的具体饰演者，这样才可能在剧本投拍时自由地遴选任何适合的演员，而非绑定在刘德华一人身上。通过以上的分析，我们知道需要为该剧本主人公革离定义一个接口： 

```java
public class MoAttack {
   public void cityGateAsk()
   {
        //①引入革离角色接口
	   GeLi geli = new LiuDeHua(); 
       
        //②通过接口开展剧情
	   geli.responseAsk("墨者革离！");  
   }
}
```

在①处引入了剧本的角色——革离，剧本的情节通过角色展开，在拍摄时角色由演员饰演，如②处所示。因此墨攻、革离、刘德华三者的类图关系如图 3-2所示： 

![](http://dl2.iteye.com/upload/attachment/0066/7640/14d800d6-3505-3ca3-b21b-01194aaf0a83.jpg)

可是，从图3-2中，我们可以看出MoAttack同时依赖于GeLi接口和LiuDeHua类，并没有达到我们所期望的剧本仅依赖于角色的目的。但是角色最终必须通过具体的演员才能完成拍摄，如何让LiuDeHua和剧本无关而又能完成GeLi的具体动作呢？当然是在影片投拍时，导演将LiuDeHua安排在GeLi的角色上，导演将剧本、角色、饰演者装配起来（图3-3）。 

![](http://dl2.iteye.com/upload/attachment/0066/7642/24018187-7df1-3657-8464-545eabdf456a.jpg)

通过引入导演，使剧本和具体饰演者解耦了。对应到软件中，导演像是一个装配器，安排演员表演具体的角色。 

   现在我们可以反过来讲解IoC的概念了。IoC（Inverse of Control）的字面意思是控制反转，它包括两个内容： 

- 其一是控制

- 其二是反转

**那到底是什么东西的“控制”被“反转”了呢**？对应到前面的例子，“控制”是指选择GeLi角色扮演者的控制权；“反转”是指这种控制权从《墨攻》剧本中移除，转交到导演的手中。对于软件来说，即是某一接口具体实现类的选择控制权从调用类中移除，转交给第三方决定。 

从注入方法上看，主要可以划分为三种类型：构造函数注入、属性注入和接口注入。Spring支持构造函数注入和属性注入。下面我们继续使用以上的例子说明这三种注入方法的区别。 

1. **构造函数注入**

在构造函数注入中，我们通过调用类的构造函数，将接口实现类通过构造函数变量传入：

```java
public class MoAttack {
   private GeLi geli;
   //①注入革离的具体扮演者
   public MoAttack(GeLi geli){ 
	   this.geli = geli;
   }
	public void cityGateAsk(){
	   geli.responseAsk("墨者革离！");
   }
}
```

 MoAttack的构造函数不关心具体是谁扮演革离这个角色，只要在①处传入的扮演者按剧本要求完成相应的表演即可。角色的具体扮演者由导演来安排:

```java
public class Director {
   public void direct(){
        //①指定角色的扮演者
	   GeLi geli = new LiuDeHua();  

        //②注入具体扮演者到剧本中
	   MoAttack moAttack = new MoAttack(geli); 
	   moAttack.cityGateAsk();
   }
}
```

在①处，导演安排刘德华饰演革离的角色，并在②处，将刘德华“注入”到墨攻的剧本中，然后开始“城门叩问”剧情的演出工作。 

2. **属性注入**

有时，导演会发现，虽然革离是影片《墨攻》的第一主角，但并非每个场景都需要革离的出现，在这种情况下通过构造函数注入相当于每时每刻都在革离的饰演者在场，可见并不妥当，这时可以考虑使用属性注入。属性注入可以有选择地通过Setter方法完成调用类所需依赖的注入，更加灵活方便： 

```java
public class MoAttack {
	private GeLi geli;
     //①属性注入方法
	public void setGeli(GeLi geli) {  
		this.geli = geli;
	}
	public void cityGateAsk() {
		geli.responseAsk("墨者革离");
	}
}
```

MoAttack在①处为geli属性提供一个Setter方法，以便让导演在需要时注入geli的具体扮演者。 

```java
public class Director {
   public void direct(){
	   GeLi geli = new LiuDeHua();
	   MoAttack moAttack = new MoAttack();

        //①调用属性Setter方法注入
	   moAttack.setGeli(geli); 
	   moAttack.cityGateAsk();
   }
}
```

和通过构造函数注入革离扮演者不同，在实例化MoAttack剧本时，并未指定任何扮演者，而是在实例化MoAttack后，在需要革离出场时，才调用其setGeli()方法注入扮演者。按照类似的方式，我们还可以分别为剧本中其他诸如梁王、巷淹中等角色提供注入的Setter方法，这样，导演就可以根据所拍剧段的不同，注入相应的角色了。 

3. **接口注入**

将调用类所有依赖注入的方法抽取到一个接口中，调用类通过实现该接口提供相应的注入方法。为了采取接口注入的方式，必须先声明一个ActorArrangable接口： 

```java
public interface ActorArrangable {
   void injectGeli(GeLi geli);
}
```

然后，MoAttack实现ActorArrangable接口提供具体的实现： 

```java
public class MoAttack implements ActorArrangable {
	private GeLi geli;
     //①实现接口方法
	public void injectGeli (GeLi geli) {  
		this.geli = geli;		
	}
	public void cityGateAsk() {
		geli.responseAsk("墨者革离");
	}
}
```

Director通过ActorArrangable的injectGeli()方法完成扮演者的注入工作。

```java
public class Director {
   public void direct(){
	   GeLi geli = new LiuDeHua();
	   MoAttack moAttack = new MoAttack();
	   moAttack. injectGeli (geli);
	   moAttack.cityGateAsk();
   }
}
```

由于通过接口注入需要额外声明一个接口，增加了类的数目，而且它的效果和属性注入并无本质区别，因此我们不提倡采用这种方式。

- **通过容器完成依赖关系的注入**

虽然MoAttack和LiuDeHua实现了解耦，MoAttack无须关注角色实现类的实例化工作，但这些工作在代码中依然存在，只是转移到Director类中而已。假设某一制片人想改变这一局面，在选择某个剧本后，希望通过一个“海选”或者第三中介机构来选择导演、演员，让他们各司其职，那剧本、导演、演员就都实现解耦了。 
   所谓媒体“海选”和第三方中介机构在程序领域即是一个第三方的容器，它帮助完成类的初始化与装配工作，让开发者从这些底层实现类的实例化、依赖关系装配等工作中脱离出来，专注于更有意义的业务逻辑开发工作。这无疑是一件令人向往的事情，Spring就是这样的一个容器，它通过配置文件或注解描述类和类之间的依赖关系，自动完成类的初始化和依赖注入的工作。下面是Spring配置文件的对以上实例进行配置的配置文件片断： 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
        <!--①实现类实例化-->
   <bean id="geli" class="LiuDeHua"/>
   <bean id="moAttack" class="com.baobaotao.ioc.MoAttack" 
         p:geli-ref="geli"/><!--②通过geli-ref建立依赖关系-->
</beans>
```

通过new XmlBeanFactory(“beans.xml”)等方式即可启动容器。在容器启动时，Spring根据配置文件的描述信息，自动实例化Bean并完成依赖关系的装配，从容器中即可返回准备就绪的Bean实例，后续可直接使用之。 

#### 什么是依赖注入（Dependency Injection, DI）

IoC是一个很大的概念,可以用不同的方式实现。其主要形式有两种：

- **依赖查找：**容器提供回调接口和上下文条件给组件。EJB和Apache Avalon 都使用这种方式。这样一来，组件就必须使用容器提供的API来查找资源和协作对象，仅有的控制反转只体现在那些回调方法上（也就是上面所说的 类型1）：容器将调用这些回调方法，从而让应用代码获得相关资源。

- **依赖注入：**组件不做定位查询，只提供普通的Java方法让容器去决定依赖关系。容器全权负责的组件的装配，它会把符合依赖关系的对象通过JavaBean属性或者构造函数传递给需要的对象。通过JavaBean属性注射依赖关系的做法称为设值方法注入(Setter Injection)；将依赖关系作为构造函数参数传入的做法称为[构造器注入（Constructor Injection） 

因为IoC确实不够开门见山，因此业界曾进行了广泛的讨论，最终软件界的泰斗级人物Martin Fowler提出了DI（依赖注入：Dependency Injection）的概念用以代替IoC，即让调用类对某一接口实现类的依赖关系由第三方（容器或协作类）注入，以移除调用类对某一接口实现类的依赖。“依赖注入”这个名词显然比“控制反转”直接明了、易于理解。 

#### 如何工作

##### 容器

在基于Spring的应用中，你的应用对象生存于Spring容 器（container）中。Spring容器负责创建对象， 装 配 它 们， 配 置 它 们 并 管 理 它 们 的 整 个 生 命 周 期， 从 生 存 到 死 亡。

容器(Container)可以归为两类：

- bean工厂（bean factory）
- 应用上下文（application context）



#### 总结

依赖注入的整个流程是：

- 编写依赖注入的方法（构造器注入、属性注入、接口注入）
- 装配bean（把多种解耦合的具体方法结合在一起）
- 调用应用上下文（Application Context）；把bean组装起来，让bean发挥作用；即实现注入的过程。一般在要实现功能（一般是在Main函数里）的时候使用）

我们可以看到，第一步是写**逻辑**，第二步是写如何将这些逻辑结合在一起的**方法**，第三步是**执行**第二步这些方法的方法。

### 什么是面向切面编程(Aspect Oriented Programming, AOP)

**Reference：**

[明明白白AOP(你没有理由不心领神会!)](http://www.iteye.com/topic/1123398)

编程语言最终极的目标就是能以更自然、更灵活的方式模拟世界，从原始机器语言到过程语言再到面向对象的语言，编程语言一步步地用更自然、更灵活的方式描述软件。AOP是软件开发思想发展到一定阶段的产物，但AOP的出现并不是要完全替代OOP，而仅是作为OOP的有益补充。虽然AOP作为一项编程技术已经有多年的历史，但一直长时间停留在学术领域，直到近几年，AOP才作为一项真正的实用技术在应用领域开疆扩土。需要指出的是AOP的应用场合是受限的，它一般只适合于那些具有横切逻辑的应用场合：如性能监测、访问控制、事务管理以及日志记录（虽然有很多文章用日志记录作为讲解AOP的实例，但很多人认为很难用AOP编写实用的程序日志，笔者对此观点非常认同）。不过，这丝毫不影响AOP作为一种新的软件开发思想在软件开发领域所占有的地位。 

AOP是Aspect Oriented Programing的简称，最初被译为“面向方面编程”，这个翻译向来为人所诟病，但是由于先入为主的效应，受众广泛，所以这个翻译依然被很多人使用，但我们更倾向于用“面向切面编程”的译法，因为它更加达意。 

按照软件重构思想的理念，如果多个类中出现相同的代码，应该考虑定义一个共同的抽象类，将这些相同的代码提取到抽象类中。比如Horse、Pig、Camel这些对象都有run()、eat()的方法，通过引入一个包含这两个方法抽象的Animal父类，Horse、Pig、Camel就可以通过继承Animal复用到run()和eat()的方法。通过引入父类消除多个类中重复代码的方式在大多情况下是可行的，但世界并非永远这样简单，请看下面论坛管理业务类的代码： 

```java
package com.baobaotao.concept;
public class ForumService {
	private TransactionManager transManager;
	private PerformanceMonitor pmonitor;
	private TopicDao topicDao;
	private ForumDao forumDao;

	public void removeTopic(int topicId) {
		pmonitor.start();//①-1性能监控开始
		transManager.beginTransaction();//②-1 事务处理开始

		topicDao.removeTopic(topicId); //③-1 业务逻辑

		transManager.commit();//②-1事务处理结束
		pmonitor.end();//①-2 性能监控结束
	}
	public void createForum(Forum forum) {
		pmonitor.start();//①-1性能监控开始
		transManager.beginTransaction();//②-1 事务处理开始

		forumDao.create(forum); //③-2 业务逻辑

		transManager.commit();//②-1事务处理结束
		pmonitor.end();//①-2 性能监控结束
	}
	…
}
```

代码中①的代码是方法性能监视代码，它在方法调用前启动，在方法调用返回前结束，并在内部记录性能监视的结果信息。 

而②的代码是事务开始和事务提交的代码。我们发现③处的业务代码淹没在重复化非业务性的代码之中，性能监视和事务管理这些非业务性代码葛藤缠树般包围着业务性代码。 

如图6-1所示，假设我们将ForumService业务类看成一段圆木，将removeTopic()和createForum()方法分别看成圆木的一截，我们会发现性能监视和事务管理的代码就好像一个年轮，而业务代码是圆木的树心，这也正是横切代码概念的由来。 

![img](http://dl.iteye.com/upload/attachment/0067/5918/e32d8807-7b85-39ee-8f32-42b86f3aa6ca.jpg) 

我们无法通过抽象父类的方式消除以上所示的重复性横切代码，因为这些横切逻辑依附在业务类方法的流程中，它们不能转移到父类中去。 

AOP独辟蹊径通过横向抽取机制为这类无法通过纵向继承体系进行抽象的重复性代码提供了解决方案。对于习惯了纵向抽取的开发者来说，可能不容易理解横向抽取方法的工作机制，因为Java语言本身不直接提供这种横向抽象的能力，我们暂把具体实现放在一旁，先通过图解的方式归纳出AOP的解决思路，如图6-2所示。 

![img](http://dl.iteye.com/upload/attachment/0067/5920/f15fad79-a256-3dca-beb7-430053db8a80.jpg) 

从图6-2中，我们可以看出AOP希望将这些分散在各个业务逻辑代码中的相同代码，通过横向切割的方式抽取到一个独立的模块中，还业务逻辑类一个清新的世界。 

当然，我们知道将这些重复性的横切逻辑独立出来是很容易的，但如何将这些独立的逻辑融合到业务逻辑中完成和原来一样的业务操作，这才是事情的关键，也正是AOP要解决的主要问题。 

### Bean装配

#### 什么是Bean

![Bean结构](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/Bean结构.png)

1. Java面向对象，对象有方法和属性，那么就需要对象实例来调用方法和属性（即实例化）；
2. 凡是有方法或属性的类都需要实例化，这样才能具象化去使用这些方法和属性；
3. 规律：**凡是子类及带有方法或属性的类都要加上注册Bean到Spring IoC的注解**；
4. 把Bean理解为类的代理或代言人（实际上确实是通过反射、代理来实现的），这样它就能代表类拥有该拥有的东西了。

#### 如何装配Bean

创 建 应 用 对 象 之 间 关 联 关 系 的 传 统 方 法（ 通 过 构 造 器 或 者 查 找） 通 常 会 导 致 结 构 复 杂 的 代 码， 这 些 代 码 很 难 被 复 用 也 很 难 进 行 单 元 测 试。 如 果 情 况 不 严 重 的 话， 这 些 对 象 所 做 的 事 情 只 是 超 出 了 它 应 该 做 的 范 围； 而 最 坏 的 情 况 则 是， 这 些 对 象 彼 此 之 间 高 度 耦 合， 难 以 复 用 和 测 试。 

在Spring中，对象无需自己查找或创建与其所关联的其他对象。相反，容器负责把需要相互协作的对象引用赋予各个对象。例如，一个订单管理组件需要信用卡认证组件，但它不需要自己创建信用卡认证组件。订单管理组件只需要表明自己两手空空，容器就会主动赋予它一个信用卡认证组件。

创建应用对象之间协作关系的行为通常称为装配（wiring，这也是依赖注入（ DI）的本质。

spring为装配bean提供了三种机制：

- 在XML中显示配置
- 在Java中显示配置
- 隐式的bean发现机制和自动装配

在使用中，我们应该尽可能使用自动装配的机制，其次是java显式配置，最后才是XML显式配置。

##### 自动装配

```java
//Config配置
@Configuration
@ComponentScan


//bean配置
@Component

```

##### 显示装配



##### 装配歧义

```@Qualifier```

##### Bean的作用域

在默认情况下，Spring应用上下文中所有bean都是作为以单例（singleton）的形式创建的。也就是说，不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例。

但是很明显，在某些情况下，我们需要变化的Bean。举个例子，在一个电子商务应用中，购物车是一个Bean，如果Bean只是以单例的形式出现，那么一个用户把他要买的拖把放入购物车，所有用户的购物车内都会出现拖把。所以我们需要给每一个用户创建独立的Bean。

Bean的作用域有以下几种：

- 单例（Singleton）：在整个应用中，只创建一个Bean实例。

- 原 型（ Prototype）： 每 次 注 入 或 者 通 过 Spring 应 用 上 下 文 获 取 的 时 候， 都 会 创 建 一 个 新 的 bean 实 例。
-  会 话（ Session）：在Web应用中，为每个会话创 建一个bean实例。 
- 请 求（ Rquest）：在Web应用中，为每个请求创建一个bean实例。

如果我要将bean声明为原型，那么：

```@Scope( ConfigurableBeanFactory.SCOPE_PROTOTYPE)```

## Spring MVC

![image-20181011235247061](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/SpringMVC.png)

### 什么是Spring MVC

**Spring MVC**框架是一个开源的Java平台，为开发强大的基于Java的Web应用程序提供全面的基础架构支持非常容易和非常快速。

**Spring web MVC**框架提供了MVC(模型 - 视图 - 控制器)架构和用于开发灵活和松散耦合的Web应用程序的组件。 **MVC**模式导致应用程序的不同方面(输入逻辑，业务逻辑和UI逻辑)分离，同时提供这些元素之间的松散耦合。

![MVC结构](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/MVC结构.png) 

- **模型(Model)**封装了应用程序数据，通常它们将由`POJO`类组成。
- **视图(View)**负责渲染模型数据，一般来说它生成客户端浏览器可以解释HTML输出。
- **控制器(Controller)**负责处理用户请求并构建适当的模型，并将其传递给视图进行渲染。







![](https://wizardforcel.gitbooks.io/gen-wo-xue-spring/content/img/529024df9d2b0d1e62d8054a86d866c9__1.JPG)**具体执行步骤如下：**

1. 首先用户发送请求————>前端控制器，前端控制器根据请求信息（如URL）来决定选择哪一个页面控制器进行处理并把请求委托给它，即以前的控制器的控制逻辑部分；图2-1中的1、2步骤；

2. 页面控制器接收到请求后，进行功能处理，首先需要收集和绑定请求参数到一个对象，这个对象在Spring Web MVC中叫命令对象，并进行验证，然后将命令对象委托给业务对象进行处理；处理完毕后返回一个ModelAndView（模型数据和逻辑视图名）；图2-1中的3、4、5步骤；

3. 前端控制器收回控制权，然后根据返回的逻辑视图名，选择相应的视图进行渲染，并把模型数据传入以便视图渲染；图2-1中的步骤6、7；

4. 前端控制器再次收回控制权，将响应返回给用户，图2-1中的步骤8；至此整个结束。

**问题：**

1. 请求如何给前端控制器？

2. 前端控制器如何根据请求信息选择页面控制器进行功能处理？

3. 如何支持多种页面控制器呢？

4. 如何页面控制器如何使用业务对象？

5. 页面控制器如何返回模型数据？

6. 前端控制器如何根据页面控制器返回的逻辑视图名选择具体的视图进行渲染？

7. 不同的视图技术如何使用相应的模型数据？



![](https://wizardforcel.gitbooks.io/gen-wo-xue-spring/content/img/57ea9e7edeebd5ee2ec0cf27313c5fb6__2.JPG)

**核心架构的具体流程步骤如下：**

1. 首先用户发送请求——>DispatcherServlet，前端控制器收到请求后自己不进行处理，而是委托给其他的解析器进行处理，作为统一访问点，进行全局的流程控制；

2. DispatcherServlet——>HandlerMapping， HandlerMapping将会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器（页面控制器）对象、多个HandlerInterceptor拦截器）对象，通过这种策略模式，很容易添加新的映射策略；

3. DispatcherServlet——>HandlerAdapter，HandlerAdapter将会把处理器包装为适配器，从而支持多种类型的处理器，即适配器设计模式的应用，从而很容易支持很多类型的处理器；

4. HandlerAdapter——>处理器功能处理方法的调用，HandlerAdapter将会根据适配的结果调用真正的处理器的功能处理方法，完成功能处理；并返回一个ModelAndView对象（包含模型数据、逻辑视图名）；

5. ModelAndView的逻辑视图名——> ViewResolver， ViewResolver将把逻辑视图名解析为具体的View，通过这种策略模式，很容易更换其他视图技术；

6. View——>渲染，View会根据传进来的Model模型数据进行渲染，此处的Model实际是一个Map数据结构，因此很容易支持其他视图技术；

7. 返回控制权给DispatcherServlet，由DispatcherServlet返回响应给用户，到此一个流程结束。



到此，再来看我们**前边提出的问题**

1. 请求如何给前端控制器？这个应该在web.xml中进行部署描述，在HelloWorld中详细讲解。

2. 前端控制器如何根据请求信息选择页面控制器进行功能处理？ 我们需要配置HandlerMapping进行映射

3. 如何支持多种页面控制器呢？配置HandlerAdapter从而支持多种类型的页面控制器

4. 如何页面控制器如何使用业务对象？可以预料到，肯定利用Spring IoC容器的依赖注入功能

5. 页面控制器如何返回模型数据？使用ModelAndView返回

6. 前端控制器如何根据页面控制器返回的逻辑视图名选择具体的视图进行渲染？ 使用ViewResolver进行解析

7. 不同的视图技术如何使用相应的模型数据？ 因为Model是一个Map数据结构，很容易支持其他视图技术



在此我们可以看出具体的**核心开发步**骤：

1. DispatcherServlet在web.xml中的部署描述，从而拦截请求到Spring Web MVC

2. HandlerMapping的配置，从而将请求映射到处理器

3. HandlerAdapter的配置，从而支持多种类型的处理器

4. ViewResolver的配置，从而将逻辑视图名解析为具体视图技术

5. 处理器（页面控制器）的配置，从而进行功能处理