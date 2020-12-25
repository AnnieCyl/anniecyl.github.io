title: Design Patterns
date: 2020-12-14 10:56:08
tags:
- programming

---
# 设计模式学习笔记

面向对象六大设计原则：
* 单一职责原则（Single Responsibility Principle，简称SRP）
* 开闭原则（Open Close Principle，简称OCP）：对扩展开放，对修改关闭。
* 接口隔离原则（InterfaceSegregation Principles，简称ISP）：客户端不应该依赖它不需要的接口；一个类对另一个类的依赖应该建立在最小的接口上。
* 里氏替换原则（Liskov Substitution Principle，简称LSP）：所有引用基类的地方必须能透明地使用其子类的对象。
* 依赖倒置原则（Dependence Inversion Principle，简称DIP）：模块间通过接口依赖，实现类之间不发生直接的依赖关系。
* 迪米特原则（Law of Demeter，简称LOD）也称为最少知识原则（Least Knowledge Principle）：一个对象应该对其他对象有最少的了解，也就是关于如何松耦合，一个类应该对自己需要耦合或调用的类知道得最少。

## 创建型模式

### 1. 工厂方法（Factory Method）

简单工厂方法：一个工厂类根据不同的参数创建不同的产品实例对象返回。当有新产品类时，需要修改工厂类中生成实例对象的方法，将这个新产品的判断添加进去来创建新产品的实例对象返回。所以简单工厂方法不符合`开放-封闭原则`。

![simple-factory](/img/2020-12-14-design-patterns/simple-factory.png)

工厂方法：一个抽象工厂类，有不同的具体工厂类继承实现。每个具体工厂类创建返回不同的具体产品对象。当有新产品需要添加的时候，只要添加对应的工厂类和新产品类即可，不用去修改已有的工厂类和产品类。所以工厂方法模式符合`开放-封闭原则`。

![factory-method](/img/2020-12-14-design-patterns/factory-method.png)

https://www.hollischuang.com/archives/3709

### 2. 抽象工厂（Abstract Factory）

与工厂方法不同的是，抽象工厂针对的是生产一组相关的产品, 即一个产品族。

* 产品等级结构：产品等级结构即产品的继承结构，如一个抽象类是电视机，取子类有海尔电视机、长虹电视机，则抽象电视机与具体品牌的电视机之间构成了一个产品等级结构。

* 产品族：在抽象工厂模式中，产品族是指同一个工厂生产的，位于不同产品等级结构中的一组产品，如海尔电器工厂生产的海尔电视机、海尔电冰箱，海尔电视机位于电视机产品等级结构中，海尔电冰箱位于电冰箱产品等级结构中。

![abstract-factory](/img/2020-12-14-design-patterns/abstract-factory.png)

### 3. 生成器（Builder Pattern）

也称为建造者模式，将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。简单来说就是，相同的过程可以创建不同的产品。

![builder](/img/2020-12-14-design-patterns/builder.png)

https://refactoringguru.cn/design-patterns/builder

使用场景：当一个类的构造函数参数个数超过4个，而且这些参数有些是可选的参数，考虑使用构造者模式。在 Android 中应用十分广泛，比如 AlerDialog：

```java
new AlertDialog.Builder(this).setTitle("这是标题")
        .setMessage("这是 Message")
        .setPositiveButton("确定", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {

            }
        })
        .setNegativeButton("取消", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {

            }
        })
        .create()
        .show();
```

### 4. 原型（Prototype Pattern）

原型模式是一种对象创建型模式，不是利用类来生成实例，而是通过实例来生成实例。换句话说原型模式就是通过复制现有实例来创建新的实例，无须知道相应类的信息。

使用原型模式创建对象比直接 new 一个对象更有效，因为它是直接对内存进行拷贝，数据对象的内容就全部都有了，不需要重新创建内存区域重新实例化赋值这些操作了，这样的话速度能够快很多。在一些重复创建对象的场景下，就可以使用原型模式来提高对象的创建性能。例如：循环体内创建对象时，就可以考虑用 `clone()` 的方式来实现。

原型模式的克隆分为浅克隆和深克隆。
* 浅克隆：创建一个新对象，新对象的属性和原来对象完全相同，对于非基本类型属性，仍指向原有属性所指向的对象的内存地址。
* 深克隆：创建一个新对象，属性中引用的其他对象也会被克隆，不再指向原有对象地址。

![prototype](/img/2020-12-14-design-patterns/prototype.png)

### 5. 单例（Singleton Pattern）

单例模式能够保证一个类只有一个实例，并提供一个访问该实例的全局节点。

所有单例的实现都包含以下两个相同的步骤：
* 将默认构造函数设为私有， 防止其他对象使用单例类的 new 运算符。
* 新建一个静态构建方法作为构造函数。 该函数会 “偷偷” 调用私有构造函数来创建对象， 并将其保存在一个静态成员变量中。 此后所有对于该函数的调用都将返回这一缓存对象。

![singleton](/img/2020-12-14-design-patterns/singleton.png)

## 结构型模式
### 1. 适配器（Adapter Pattern）

把一个类的接口变换成客户端所期待的另一种接口，从而使原本因为接口不匹配而无法一起工作的两个类能够在一起工作。

适配器模式的形式分为：

* 类的适配器模式

  ![adapter-class](/img/2020-12-14-design-patterns/adapter-class.png)

  ```java
  public interface Target {
      void request();
  }
  
  public class Adaptee {
      public void specificRequest() {
          log.info("specificRequest");
      }
  }
  
  public class Adapter extends Adaptee implements Target {
  
      @Override
      public void request() {
          
          specificRequest();
          // TODO: Other operations
          ... ...
      }
  }
  ```

* 对象的适配器模式

  ![adapter-object](/img/2020-12-14-design-patterns/adapter-object.png)

  ```java
  public interface Target {
      void request();
  }
  
  public class Adaptee {
      public void specificRequest() {
          log.info("specificRequest");
      }
  }
  
  public class Adapter implements Target {
      private Adaptee adaptee;
      
      public Adapter(Adaptee adaptee) {
          this.adaptee = adaptee;
      }
  
      @Override
      public void request() {
          
          adaptee.specificRequest();
          // TODO: Other operations
          ... ...
      }
  }
  ```

一般情况下尽可能多的使用对象适配器模式，因为其实现了解耦合。

### 2. 桥接（Bridge Pattern）

如果软件系统中某个类存在两个独立变化的维度，通过桥接模式可以将这两个维度分离出来，使两者可以独立扩展，让系统更加符合“单一职责原则”。比如手机可以从两个维度进行变化，一个是品牌，一个是内存。此时我们就可以通过桥接模式将这两个维度分离开来，每一个维度都可以独立扩展。比如说手机品牌，可以又出现了苹果、三星、锤子等等。内存方面又可以生产 10G、16G 的了。

桥接模式有以下几种角色：

* （抽象部分）抽象角色（Abstraction）：抽象的定义，并保存一个 Implementor 对象的引用。

* （抽象部分）扩展抽象角色（RefinedAbstraction）： 拓展 Abstraction。

* （实现部分）抽象实现角色（Implementor）： 定义实现类的接口，提供基本操作，其实现交给子类实现。

* （实现部分）具体实现角色（ConcreteImplementor）： 实现 Implementor 接口，在程序运行时，子类对象将替换其父类对象，提供给 Abstraction 具体的业务操作方法。

![bridge](/img/2020-12-14-design-patterns/bridge.png)

```java
// 抽象角色（Abstraction）
public abstract class Abstraction {
    private Implementor implementor;

    protected void operation(){
        implementor.request();
    }
    
    public Implementor getImplementor() {
        return implementor;
    }

    public void setImplementor(Implementor implementor) {
        this.implementor = implementor;
    }
}

// 扩展抽象角色（RefinedAbstraction）
public class RefinedAbstraction extends Abstraction {
    @Override
    protected void operation() {
        super.operation();
        // TODO: Other operations
        ... ...
    }
}

// 抽象实现角色（Implementor）
public interface Implementor {
    public void request();
}

// 具体实现角色 A
public class ConcreateImplementorA implements Implementor {
    @Override
    public void request() {
        System.out.println("This is concreteImplementorA's request...");
    }
}

// 具体实现角色 B
public class ConcreateImplementorB implements Implementor {
    @Override
    public void operation() {
        System.out.println("This is concreteImplementorB's request...");
    }
}

// 桥接模式测试代码
public class BridgeTest {
    public static void main(String[] args) {
        Abstraction abstraction = new RefinedAbstraction();

        // 调用第一个实现类
        abstraction.setImplementor(new ConcreateImplementorA());
        abstraction.operation();

        // 调用第二个实现类
        abstraction.setImplementor(new ConcreateImplementorB());
        abstraction.operation();

    }
}
```
### 3. 组合（Composite Pattern）

组合多个对象形成**树形**结构以表示具有 "整体—部分" 关系的层次结构。组合模式对单个对象（即叶子对象）和组合对象（即容器对象）的使用具有一致性。包含以下角色：

* 抽象构件（Component）：它可以是接口或抽象类，为叶子构件和容器构件对象声明接口，在该角色中可以包含所有子类共有行为的声明和实现。在抽象构件中定义了访问及管理它的子构件的方法，如增加子构件、删除子构件、获取子构件等。

* 叶子构件（Leaf）：它在组合结构中表示叶子节点对象，叶子节点没有子节点，它实现了在抽象构件中定义的行为。对于那些访问及管理子构件的方法，可以通过异常等方式进行处理。

* 容器构件（Composite）：它在组合结构中表示容器节点对象，容器节点包含子节点，其子节点可以是叶子节点，也可以是容器节点，它提供一个集合用于存储子节点，实现了在抽象构件中定义的行为，包括那些访问及管理子构件的方法，在其业务方法中可以递归调用其子节点的业务方法。

组合模式分为透明式的组合模式和安全式的组合模式。

* 透明方式：在该方式中，由于抽象构件声明了所有子类中的全部方法，所以客户端无须区别叶子对象和容器对象，对客户端来说是透明的。但其缺点是：叶子构件本来没有 `add()`、`remove()` 及 `getChild()` 方法，却要实现它们（空实现或抛异常），这样会带来一些安全性问题。

  ![composite-expose-all-methods](/img/2020-12-14-design-patterns/composite-expose-all-methods.png)

  ```java
  //抽象构件
  interface Component {
      public void add(Component c);
  
      public void remove(Component c);
  
      public Component getChild(int i);
  
      public void operation();
  }
  
  //叶子构件
  class Leaf implements Component {
      private String name;
  
      public Leaf(String name) {
          this.name = name;
      }
  
      public void add(Component c) {
      }
  
      public void remove(Component c) {
      }
  
      public Component getChild(int i) {
          return null;
      }
  
      public void operation() {
          System.out.println("树叶" + name + "：被访问！");
      }
  }
  
  //容器构件
  class Composite implements Component {
      private ArrayList<Component> children = new ArrayList<Component>();
  
      public void add(Component c) {
          children.add(c);
      }
  
      public void remove(Component c) {
          children.remove(c);
      }
  
      public Component getChild(int i) {
          return children.get(i);
      }
  
      public void operation() {
          for (Object obj : children) {
              ((Component) obj).operation();
          }
      }
  }
  
  // 组合模式测试代码
  public class CompositePatternTest {
      public static void main(String[] args) {
          Component c0 = new Composite();
          Component c1 = new Composite();
          Component leaf1 = new Leaf("1");
          Component leaf2 = new Leaf("2");
          Component leaf3 = new Leaf("3");
          c0.add(leaf1);
          c0.add(c1);
          c1.add(leaf2);
          c1.add(leaf3);
          c0.operation();
          c1.operation();
      }
  }
  ```

* 安全方式：在该方式中，将管理子构件的方法移到容器构件中，抽象构件和叶子构件没有对子对象的管理方法，这样就避免了上一种方式的安全性问题，但由于叶子和容器构件有不同的接口，客户端在调用时要知道树叶对象和树枝对象的存在，所以失去了透明性。

  ![composite-expose-leaf-methods](/img/2020-12-14-design-patterns/composite-expose-leaf-methods.png)

### 4. 装饰器（Decorator Pattern）

动态地给一个对象添加一些额外的职责，就增加功能来说，装饰器模式比生成子类更为灵活；它允许向一个现有的对象添加新的功能，同时又不改变其结构，相当于对现有的对象进行了一个包装。

Decorator 模式的目的就是把一个一个的附加功能，用 Decorator 的方式给一层一层地累加到原始数据源上，最终，通过组合获得我们想要的功能。例如给 `FileInputStream` 增加缓冲和解压缩功能，用 Decorator 模式写出来如下： 

```java
// 创建原始的数据源:
InputStream fis = new FileInputStream("test.gz");
// 增加缓冲功能:
InputStream bis = new BufferedInputStream(fis);
// 增加解压缩功能:
InputStream gis = new GZIPInputStream(bis);
```

装饰器模式组成：

* 抽象构件角色（Component）： 定义可以动态添加任务的对象的接口

* 具体构件角色（ConcreteComponent）：定义一个要被装饰器装饰的对象，即 Component 的具体实现

* 抽象装饰器（Decorator）：持有一个构件（Component）对象的实例，并定义一个与抽象构件接口一致的接口。

* 具体装饰器角色（ConcreteDecorator）：向构件对象添加新的职责

  ![decorator](/img/2020-12-14-design-patterns/decorator.png)

```java
// 抽象构件角色
public interface Component {
    public void operation();
}

// 具体构件角色
public class ConcreteComponent implements Component {
    @Override
    public void operation() {
        System.out.println("装饰器模式。。。。。");
    }
}

// 装饰器角色
public class Decorator implements Component {

    private Component component;

    public Decorator(Component component)
    {
        this.component = component;
    }
    
    @Override
    public void operation() {
        component.toDoSth();
    }
}

// 具体装饰角色 A
public class ConcreteDecoratorA extends Decorator {

    public ConcreteDecoratorA(Component component) {
        super(component);
    }
    
    public void operation()
    {
        super.operation();
        
        this.newFunA();
    }
    
    private void newFunA() {
        System.out.println("新功能A");
    }
}

package com.design.decorator;

// 具体装饰角色 B
public class ConcreteDecoratorB extends Decorator {

    public ConcreteDecoratorB(Component component) {
        super(component);
    }
    
    public void operation()
    {
        super.operation();
        
        this.newFunB();
    }
    
    private void newFunB() {
        System.out.println("新功能B");
    }
}

package com.design.decorator;

// 装饰模式测试代码
public class TestDecorator {

    public static void main(String[] args) {
        Component component = new ConcreteComponent();
        
        Component component1 = new ConcreteDecoratorA(component);
        component1.operation();
        
        Component component2 = new ConcreteDecoratorB(component1);
        component2.operation();
    }
}
```

装饰模式通常在以下几种情况使用。

- 当需要给一个现有类添加附加职责，而又不能采用生成子类的方法进行扩充时。例如，该类被隐藏或者该类是终极类或者采用继承方式会产生大量的子类。
- 当需要通过对现有的一组基本功能进行排列组合而产生非常多的功能时，采用继承关系很难实现，而采用装饰模式却很好实现。
- 当对象的功能要求可以动态地添加，也可以再动态地撤销时。

### 5. 外观（Facade Pattern）

外观模式又称为门面模式，为子系统中的一组接口提供一个统一的入口。外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。如办房产证或注册一家公司，要同多个部门联系，这时要是有一个综合部门能解决一切手续问题就好了。

外观模式包含如下两个角色：

* 外观角色（Facade）：客户端可以调用它的方法，外观角色可以知道相关的（一个或者多个）子系统的功能和责任。正常情况下，它将所有从客户端发来的请求委派到相应的子系统去，传递给相应的子系统对象处理。

* 子系统角色（SubSystem）：在软件系统中可以有一个或者多个子系统角色，每一个子系统可以不是一个单独的类，而是一个类的集合，它实现子系统的功能。每一个子系统都可以被客户端直接调用，或者被外观角色调用，它处理由外观类传过来的请求；子系统并不知道外观的存在，对于子系统而言，外观角色仅仅是另外一个客户端而已。

![facade](/img/2020-12-14-design-patterns/facade.png)

```java
//外观角色
class Facade {
    private SubSystem1 obj1 = new SubSystem1();
    private SubSystem2 obj2 = new SubSystem2();
    private SubSystem3 obj3 = new SubSystem3();

    public void method() {
        obj1.method1();
        obj2.method2();
        obj3.method3();
    }
}

//子系统角色
class SubSystem1 {
    public void method1() {
        System.out.println("子系统 1 的 method1() 被调用！");
    }
}

//子系统角色
class SubSystem2 {
    public void method2() {
        System.out.println("子系统 2 的 method2() 被调用！");
    }
}

//子系统角色
class SubSystem3 {
    public void method3() {
        System.out.println("子系统 3 的 method3() 被调用！");
    }
}

// 外观模式测试代码
public class FacadePatternTest {
    public static void main(String[] args) {
        Facade f = new Facade();
        f.method();
    }
}
```

### 6. 享元（Flyweight Pattern）

“享”是共享的意思，“元”指的是元件，也就是小颗粒的东西，"享元"顾名思义便是共享小部件。享元模式以共享的方式高效地支持大量细粒度对象的重用，在享元模式中，存储这些共享实例对象的地方称为享元池（Flyweight Pool）。

在享元模式结构图中包含如下几个角色：

- 抽象享元类（Flyweight）：通常是一个接口或抽象类，在抽象享元类中声明了具体享元类公共的方法，这些方法可以向外界提供享元对象的内部数据（内部状态），同时也可以通过这些方法来设置外部数据（外部状态）。
- 具体享元类（ConcreteFlyweight）：它实现了抽象享元类，其实例称为享元对象。在具体享元类中为内部状态提供了存储空间。通常我们可以结合单例模式来设计具体享元类，为每一个具体享元类提供唯一的享元对象。
- 非共享具体享元类（UnsharedConcreteFlyweight）：并不是所有的抽象享元类的子类都需要被共享，不能被共享的子类可设计为非共享具体享元类。当需要一个非共享具体享元类的对象时可以直接通过实例化创建。
- 享元工厂类（FlyweightFactory）：享元工厂类用于创建并管理享元对象，它针对抽象享元类编程，将各种类型的具体享元对象存储在一个享元池中，享元池一般设计为一个存储“键值对”的集合（也可以是其他类型的集合），可以结合工厂模式进行设计。当用户请求一个具体享元对象时，享元工厂提供一个存储在享元池中已创建的实例或者创建一个新的实例（如果不存在的话），返回新创建的实例并将其存储在享元池中。

![flyweight](/img/2020-12-14-design-patterns/flyweight.png)

```java
// 抽象享元类
public interface Flyweight {
    String getInnerState();
    void operation(String  externalState);
}

// 具体享元类
public class ConcreteFlyweight implements Flyweight {
    private String innerState;

    public ConcreteFlyweight(String innerState){
        this.innerState = innerState;
    }

    public String getInnerState() {
        return innerState;
    }

    public void operation(String externalState) {
        System.out.println(externalState);
    }
}

// 享元工厂类
public class FlyweightFactory {
    // 定义一个 HashMap 用于存储享元对象，实现享元池
    private static final Map<String, Flyweight> FLYWEIGHTS = new ConcurrentHashMap();
    private static final FlyweightFactory INSTANCE = new FlyweightFactory();

    private FlyweightFactory(){}

    public static FlyweightFactory getInstance(){
        return INSTANCE;
    }

    public Flyweight getFlyweight(String key){
        // 如果对象存在，则直接从享元池获取
        if (FLYWEIGHTS.containsKey(key)){
            return FLYWEIGHTS.get(key);
        }else {
            // 如果对象不存在，先创建一个新的对象添加到享元池中，然后返回
            Flyweight flyweight = new ConcreteFlyweight("innerState");
            FLYWEIGHTS.put(key, flyweight);
            return flyweight;
        }
    }
}
```

### 7. 代理（Proxy Pattern）

代理模式的目标是控制对被代理对象的访问，而装饰模式是给原对象增加额外功能。

## 行为型模式
### 1. 责任链
### 2. 命令
### 3. 解释器
### 4. 迭代器
### 5. 中介
### 6. 备忘录
### 7. 观察者
### 8. 状态
### 9. 策略
### 10. 模板方法
### 11. 访问者

