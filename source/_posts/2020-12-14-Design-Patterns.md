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

### 2. 桥接



### 3. 组合
### 4. 装饰器
### 5. 外观
### 6. 享元
### 7. 代理
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

